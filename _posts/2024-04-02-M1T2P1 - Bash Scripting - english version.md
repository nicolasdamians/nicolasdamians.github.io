---
layout: post
title: "M1T2P1 - Pass2hash - English Version"
date: 2024-04-14 23:25:00 -0400
categories:
    - posts
tags:
    - Master
    - Homework
    - M1
    - script
    - bash
    - en
---

## M1.Task 2. Bash Scripting 

### Professional Master in Offensive Security (OSCP) (03/26/2024 - 03/31/2025)

- **Student:** Nicolas Sadofschi | gen0ne | [https://xargs.cat](https://xargs.cat)

# Task Statement

This task consists of two parts:

In the first part, you need to create a script that will receive two arguments:

    - The first argument is the path to the hashes file (available on the campus). The hashes file contains a series of hashes. The hashes are in sha256 format (generated without line breaks with echo).
    - The second argument is a wordlist file (you can reduce it or create your own).

The idea is to read plain text words from your wordlist (typical passwords), hash them using SHA256 (without line breaks), and compare them with the hashes in the hashes file.

Objectives: 

    - Find at least one word in the hashes file.
    - More importantly: create the script that automates this entire search.

# Part 1 
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
# Versión: 2.1
# Master: Master Profesional en Seguridad Ofensiva (OSCP)
# Módulo: M1
# Tarea: Tarea 2 - Bash Scripting ; Primera parte
#
# Historial de cambios:
# 14/04/2024 22:15 Agregada esta cabecera.
# 14/04/2024 22:30 Corregidos errores ortográficos.
# 15/04/2024 01:50 Creada función hashfunc ; cambiado orden, implementada evaluación de dos resultados
# 15/04/2024 02:20 Errores ortográficos 
# 17/04/2024 21:50 Creada funcion Mensajes y utilizada en reemplazo de mensajes repetitivos
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


Mensajes(){
    if [ $# -eq 1 ]
        then
            mensaje=$1
            case $mensaje in
            1)
                echo "· El número de Passwords que coinciden con el hash es: $match"
                ;;
            2)
                echo "· El número de Passwords que NO coinciden con el hash es: $nomatch"
                ;;
            3)
                echo "· Saliendo..."
                ;;
            *)
                ;;
            esac
        else
            echo "· Error en los argumentos"
        fi
}

hashfunc(){
    match=0
    nomatch=0
    while read linea
    do
        pass2hash=$(echo -n $linea | sha256sum | cut -f1 -d' ')
        if grep -q $pass2hash $hashes 
        then
            echo "=========================================================================================="
            echo "· Resultado encontrado: la Pass '$linea' cuyo hash es $pass2hash está presente en $hashes"
            echo "=========================================================================================="
            match=$(($match +1))
            if [ $match -eq 1 ] && [ "$3" == "2coincidencias" ]
            then
                echo "· Se encontró una coincidencia. Deteniendo la búsqueda..."
                break
            fi
        else
            nomatch=$(($nomatch +1))
        fi
    done < $passwords

    if [ $match -eq 0 ]; then
        echo "· No hay coincidencias."
        Mensajes 3
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
                echo "· El argumento $hashes o $passwords no es un fichero válido"
                echo "· Uso: $0 <hashes.txt> <diccionario.txt>"
                Mensajes 3
                exit
        fi
        hashfunc "$hashes" "$passwords" "2coincidencias" 
else
        echo "· Uso: $0 <hashes.txt> <diccionario.txt>"
        exit
fi

echo
Mensajes 1
Mensajes 2
echo
echo "· El objetivo de la tarea era encontrar 1 resultado. Ya hemos encontrado 1. ¿Quieres seguir?"
read -p  "· Escribe 'Si' + Intro para continuar. Cualquier letra para salir: " seguir
echo
        if [ -n "$seguir" ] && [[ "$seguir" =~ ^[Ss][Ii]$ ]]
        then
                   hashfunc "$hashes" "$passwords"
        else
                Mensajes 3
                exit
        fi

Mensajes 1
Mensajes 2
```