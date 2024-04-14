---
layout: post
title: "M1T2 - Pass2hash - Spanish Version"
date: 2024-04-14 21:25:00 -0400
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
# Descripción: El propósito de este script es proporcionar dos archivos, uno de hashes y otro de passwords, con el fin de buscar coincidencias entre las passwords proporcionadas y los hashes.
#
# Master: Master Profesional en Seguridad Ofensiva (OSCP)
# Módulo: M1
# Tarea: Tarea 2 - Bash Scripting ; Primera parte
#
# Autor: Nicolás Damián Sadofschi | gen0ne | xargs.cat
# Fecha: 14/04/2024
# Versión: 1.0
#
# Historial de cambios:
# 14/04/2024 22:15 Agregada esta cabecera.
# 14/04/2024 22:30 Corregidos errores ortográficos.
#
#####################################################################

echo "Tarea:"
echo "El primer argumento es la ruta del archivo hashes (tenéis accesible en el campus). El archivo hashes contiene una serie de hashes. Los hashes están en sha256 (generados sin salto de línea con el echo)."
echo "El segundo argumento es un archivo de diccionario de palabras (podéis reducirlo o crearlo)."
echo "La idea es que se vayan leyendo las palabras en texto plano de vuestro diccionario (típicas contraseñas) y vayáis hasheando en SHA256 (sin salto de línea) y comparando con las palabras del archivo hashes."
echo ""
echo "Objetivos:"
echo "* Encontrar al menos una palabra en el archivo hashes."
echo "* Más importante aún: crear el script que automatice toda esta búsqueda."
echo "======================================================================"
echo ""
echo "Instrucciones: ./script <hashes> <diccionario>"
echo ""
echo "Nota: El script imprimirá las coincidencias en caso de haberlas."
echo ""
echo "Presiona Enter para continuar..."
read

if [ $# -eq 2 ] # Evaluar si se pasan dos argumentos
then
        hashes=$1 # El primer argumento es el archivo es el de hashes.txt
        passwords=$2 # El segundo argumento es el archivos de passwords.txt
        
        if [ -e $hashes ] # Evaluamos si el argumento $1 es un archivo que existe
        then
                :
        else
                echo "El argumento $hashes no es un fichero valido"
                echo "Uso: $0 <hashes.txt> <diccionario.txt>"
                exit
        fi

        if [ -e $passwords ] # Evaluamos si el argumento $2 es un archivo que existe
        then
                match=0
                nomatch=0
                while read linea # Leemos y hacemos acciones linea por linea.
                do
                pass2hash=$(echo -n $linea | sha256sum | cut -f1 -d' ') # Para cada linea sacamos el sha256, limpiamos el output y le asignamos una variable que despues comparamos.
                        if grep -q $pass2hash $hashes # Grepeamos la variable anterior, si existe printamos el resultado.
                        then
                                echo "Resultado encontrado: La contraseña '$linea' cuyo hash es $pass2hash está presente en $1"
                                match=$(($match +1))
                        else
                                nomatch=$(($nomatch +1))
                        fi
                done < $passwords


        else
                echo "El argumento $passwords no es un archivo válido"
                echo "Uso: $0 <hashes.txt> <diccionario.txt>"
                exit
        fi

else
        echo "Uso: $0 <hashes.txt> <diccionario.txt>"
        exit
fi

echo "El número de contraseñas que coinciden con el hash es: $match"
echo "El número de contraseñas que NO coinciden con el hash es: $nomatch"
```