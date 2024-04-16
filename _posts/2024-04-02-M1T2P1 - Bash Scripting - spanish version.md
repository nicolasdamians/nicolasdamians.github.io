---
layout: post
title: "M1T2P1 - Pass2hash - Spanish Version"
date: 2024-04-16 20:25:00 
categories:
    - posts
tags:
    - Master
    - Home_work
    - M1
    - script
    - bash
    - es
---

## M1.Tarea 2. Bash Scripting 

### Professional Master in Offensive Security (OSCP) (26/03/2024 - 31/03/2025)

- **Student:** Nicolas Sadofschi | gen0ne | [https://xargs.cat](https://xargs.cat)

# Enunciado de la tarea

Esta tarea tiene dos partes:

En la primera tenéis que crear un script el cual recibirá dos argumentos:

    El primer argumento es la ruta del fichero hashes (tenéis accesible en el campus). El fichero hashes es un fichero que contiene una serie de hashes. Los hashes están en sha256 (generados sin salto de línea con el echo).
    El segundo argumento es un fichero de diccionario de palabras (podéis reducirlo o crearlo).

La idea es que se vaya leyendo las palabras en texto plano de vuestro diccionario (típicas contraseñas) y vayáis hasheando en SHA256 (sin salto de línea) y comparando con las palabras del fichero hashes.

Objetivos: 

    - Encontrar al menos una palabra en el fichero hashes.
    - Más importante aún: crear el script que automatiza toda esta búsqueda.

# Parte 1 
```bash
#!/bin/bash

#####################################################################
# Script: pass2hash.sh
# Descripción:  El propósito de este script es proporcionar dos archivos, uno de hashes y otro de passwords, con el fin de buscar coincidencias entre las passwords proporcionadas y los hashes.
#               Se detendrá automaticamente al encontrar el primer resultado ya que esté es el objetivo del enunciado.
#               Dará opción a continuar si el usuario lo solicita.
#
# Autor: Nicolás Damián Sadofschi | gen0ne | xargs.cat
# Fecha: 14/04/2024
# Versión: 2.0
# Master: Master Profesional en Seguridad Ofensiva (OSCP)
# Módulo: M1
# Tarea: Tarea 2 - Bash Scripting ; Primera parte
#
# Historial de cambios:
# 14/04/2024 22:15 Agregada esta cabecera.
# 14/04/2024 22:30 Corregidos errores ortográficos.
# 15/04/2024 01:50 Creada función hashfunc ; cambiado orden, implementada evaluación de dos resultados
# 15/04/2024 02:20 Errores ortográficos 
#
# Nota: Este script se ha hecho con mucho cariño, sin embargo la opción idonea sería usar 'hashcat' ya que fue diseñado para este uso (entre otros).
# hashcat -m 1400 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt 
#
#####################################################################

echo "Tarea:"
echo "El primer argumento es la ruta del fichero hashes (tenéis accesible en el campus). El fichero hashes es un fichero que contiene una serie de hashes. Los hashes están en sha256 (generados sin salto de línea con el echo)."
echo "El segundo argumento es un fichero de diccionario de palabras (podéis reducirlo o crearlo)."
echo "La idea es que se vaya leyendo las palabras en texto plano de vuestro diccionario (típicas contraseñas) y vayáis hasheando en SHA256 (sin salto de línea) y comparando con las palabras del fichero hashes."
echo 
echo "Objetivos:"
echo "* Encontrar al menos una palabra en el fichero hashes."
echo "* Más importante aún: crear el script que automatiza toda esta búsqueda."
echo "======================================================================"
echo 
echo "Instrucciones: ./script <hashes> <diccionario>"
echo 
echo "Presiona Enter para continuar..."
read
clear

hashfunc(){
    match=0
    nomatch=0
    while read linea
    do
        pass2hash=$(echo -n $linea | sha256sum | cut -f1 -d' ')
        if grep -q $pass2hash $hashes 
        then
            echo "Resultado encontrado: la Pass '$linea' cuyo hash es $pass2hash está presente en $hashes"
            match=$(($match +1))
            if [ $match -eq 1 ] && [ "$3" == "2coincidencias" ]
            then
                echo "Se encontró una coincidencia. Deteniendo la búsqueda."
                break
            fi
        else
            nomatch=$(($nomatch +1))
        fi
    done < $passwords

    if [ $match -eq 0 ]; then
        echo "No hay coincidencias."
        exit
    fi
}

if [ $# -eq 2 ]
then
        hashes=$1 
        passwords=$2 
        if [ -e $hashes ] && [ -e $passwords ]
        then
                :
        else
                echo "El argumento $hashes o $passwords no es un fichero válido"
                echo "Uso: $0 <hashes.txt> <diccionario.txt>"
                exit
        fi
        hashfunc "$hashes" "$passwords" "2coincidencias" 
else
        echo "Uso: $0 <hashes.txt> <diccionario.txt>"
        exit
fi

echo "El número de Passwords que coinciden con el hash es: $match"
echo "El número de Passwords que NO coinciden con el hash es: $nomatch"
echo
echo "El objetivo de la tarea era encontrar 1 resultado. Ya hemos encontrado 1. ¿Quieres seguir?"
read -p  "'si' para continuar. Cualquier letra para salir: " seguir
echo
        if [ -n "$seguir" ] && [ "$seguir" == "si" ]
        then
                   hashfunc "$hashes" "$passwords"
        else
                echo "Saliendo..."
                exit
        fi

echo "El número de Passwords que coinciden con el hash es: $match"
echo "El número de Passwords que NO coinciden con el hash es: $nomatch"
```