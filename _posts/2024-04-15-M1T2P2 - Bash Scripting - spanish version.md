---
layout: post
title: "M1T2P2 - autoh4x - Spanish Version"
date: 2024-04-15 10:25:00 
categories:
    - posts
tags:
    - Master
    - bash
    - es
---

## M1.Tarea 2. Bash Scripting 

### Professional Master in Offensive Security (OSCP) (26/03/2024 - 31/03/2025)

- **Student:** Nicolas Sadofschi | gen0ne | [https://xargs.cat](https://xargs.cat)

# Enunciado de la tarea

Esta tarea tiene dos partes:

En la segunda parte tenéis que crear un script que realice lo siguiente:

- Muestre un menú con las siguientes opciones:

    – metasploit
    – dirbuster
    – nmap
    – Ofrecer Shell a través de Netcat en un puerto
    – Conectar con una Shell remota a través de Netcat
    – Salir

- El script tiene que permitir ejecutar dichas herramientas al usuario. para ello:

    Tenéis que crear funciones (para el menú y todo lo que veáis posible).
    Además, debéis ir pidiendo al usuario los parámetros que necesite para ejecutar Netcat, nmap y las otras herramientas.

Ejemplo:

    Opción Metasploit (simplemente arranca msfconsole)
    Opción dirbuster (igual que la anterior)
    Opción nmap (elegir algún tipo de escaneo y solicitar datos al usuario para la dirección IP, puerto, etc)
    Opción Ofrecer Shell. Pedir al usuario en qué puerto quiere dar la Shell en la máquina
    Opción conectar con Shell. Pedir al usuario la dirección IP remota y el puerto dónde NC deberá conectar
    Opción salir. Cierra el script.

# Parte 2
```bash
#!/bin/bash

#####################################################################
# Script: autoh4x.sh
# Descripción: El propósito de este script es proveer de utilidades basicas, creando funciones para menús y demas. He aprovechado esta tarea para orientarla a pasos que hago manualmente, principalmente inspirado por s4vitar (mkt y los nmaps que suele usar)
#
# Autor: Nicolás Damián Sadofschi | gen0ne | xargs.cat
# Fecha: 15/04/2024
# Versión: 1.0
# Master: Master Profesional en Seguridad Ofensiva (OSCP)
# Módulo: M1
# Tarea: Tarea 2 - Bash Scripting ; Segunda parte
#
# Historial de cambios:
# 15/04/2024 19:15 V 1.1
# 15/04/2024 21:30 Borrado un clear en la funcion proyecto y mejorado el output
# 17/04/2024 06:00 Añadido mensajes repetitivos a la función Mensajes.
#
# Mejoras:  - Se podria incluir una evaluacion de IP y puertos, sin embargp el script es para mi uso.
#           - Agregar nikto, wpscan, wfuzz, etc.
#
# Credits: Gracias a s4vitar por el "ExtraerPuerto y mkt"
#
# ■■■■■■■■■■■■■■■■■■■■■■
# Enunciado de la tarea:
# ■■■■■■■■■■■■■■■■■■■■■■
# En la segunda parte tenéis que crear un script que realice lo siguiente:
# 
# - Muestre un menú con las siguientes opciones:
# 
#     – metasploit
#     – dirbuster
#     – nmap
#     – Ofrecer Shell a través de Netcat en un puerto
#     – Conectar con una Shell remota a través de Netcat
#     – Salir
# 
# - El script tiene que permitir ejecutar dichas herramientas al usuario. para ello:
# 
#     Tenéis que crear funciones (para el menú y todo lo que veáis posible).
#     Además, debéis ir pidiendo al usuario los parámetros que necesite para ejecutar Netcat, nmap y las otras herramientas.
# 
# Ejemplo:
# 
#     Opción Metasploit (simplemente arranca msfconsole)
#     Opción dirbuster (igual que la anterior)
#     Opción nmap (elegir algún tipo de escaneo y solicitar datos al usuario para la dirección IP, puerto, etc)
#     Opción Ofrecer Shell. Pedir al usuario en qué puerto quiere dar la Shell en la máquina
#     Opción conectar con Shell. Pedir al usuario la dirección IP remota y el puerto dónde NC deberá conectar
#     Opción salir. Cierra el script.
#
#####################################################################

Mensajes(){
        if [ $# -eq 1 ]
        then
            mensaje=$1
            case $mensaje in

        0)
            echo
            echo -n "Proporcionar una IP o Subnet: "
            ;;
        1)
            echo
            echo -n "Introduce un Puerto o un rango de puertos, ej. 80; ej2. 1-80: "
            ;;
        2)
            echo
            echo -n "Introduce un Puerto ej. 4444: "
            ;;
        3) 
            echo
            echo "· NOTA: Este tipo de scan es recomendado para Hosts Unicos"
            echo "· Presiona enter para ver el progreso"    
            ;;
        4)
            echo
            echo "Saliendo..."
            echo
            ;;
        5)
            echo
            echo -n "Presiona enter para continuar..."
            ;;
        *)
            ;;
            esac
        else
            echo "error en los argumentos"
        fi
}

LeerIP(){
    read ip
    echo $ip
}

LeerPuerto(){
    read puerto
    echo $puerto
}

ExtraerPuerto(){
    ports="$(cat allPorts | grep -oP '\d{1,5}/open' | awk '{print $1}' FS='/' | xargs | tr ' ' ',')" # Copied from s4vitar
}

proyecto() {
    clear
    echo "Instrucciones de uso:"
    echo "====================="
    echo "· Este script necesita permisos 'sudo' para ejecutarse."
    
    proyecto_creado=false  
    
    while [ "$proyecto_creado" = false ]
    do
    read -p "Introduce un nombre para el proyecto. Nota: Se guardará en /tmp/autoh4x/<nombre proyecto>: " proyecto_name
    echo
        if [ -z "$proyecto_name" ]
        then
        :
        elif [ -d "/tmp/autoh4x/$proyecto_name" ]
        then
            echo "■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■"
            echo "· Ya existe la carpeta \"/tmp/autoh4x/$proyecto_name\", proporciona otro nombre."
            echo "■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■"
            echo
        else
            mkdir -p {/tmp/autoh4x/,/tmp/autoh4x/$proyecto_name,/tmp/autoh4x/$proyecto_name/nmap,/tmp/autoh4x/$proyecto_name/content,/tmp/autoh4x/$proyecto_name/exploits}
            proyecto_creado=true
        fi
    done
    echo
    echo "Proyecto creado con éxito en /tmp/autoh4x/$proyecto_name"
    echo
Mensajes 0
    ip=$(LeerIP)
}

menu_nmap() {
    clear
    echo "Menú de nmap"
    echo "============"
    echo
    echo "La IP objetivo es < $ip >"
    echo "Nombre de Proyecto: $proyecto_name"
    echo
    echo "1) Discovery host based on ARP"
    echo "2) Escaneo basico (-sS TCP SYN)"
    echo "3) Reconocimiento inicial; Recomendado para single hosts (Parametros: -p- --open -sS --min-rate 5000 -vvv -Pn -oG)" # Todos los puertos, solo muestra puertos abiertos, SYN TCP, Triple verbose, sin resolución y asume que está up, exporta en formato grepeable"
    echo "4) Reconocimiento especifico + Inicial; Recomendado para single hosts (Parametros: -sC -V -oN)" # Conjunto de scripts basicos, Version, exporta en Human readable"
    echo "5) Mostrar resultados punto 3 y 4"
    echo "6) Cambiar IP Objetivo"
    echo "7) Volver al menú principal"
    echo "8) Salir"

    read -p "Seleccione una opción: " opcion_nmap
    mensajenmap=$1
    case $opcion_nmap in
    1)
        echo
        echo "· Ejecutando Discovery host based on ARP..."
        echo
        nmap -sn -PR $ip
        echo
        Mensajes 5
        read
        menu_nmap
        ;;
    2)
        echo
        echo "· Ejecutando escaneo tipo -SS TCP SYN..."
        echo
        Mensajes 1
        puerto=$(LeerPuerto)
        echo
        nmap -sS $ip -p $puerto
        echo
        Mensajes 5
        read
        menu_nmap
        ;;
    3)
        echo
        echo "· Ejecutando reconocimiento inicial..."
        echo
        Mensajes 3
        nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn $ip -oG /tmp/autoh4x/$proyecto_name/nmap/allPorts 
        echo
        Mensajes 5
        read
        menu_nmap
        ;;
    4)
        echo
        echo
        if [ -f "/tmp/autoh4x/$proyecto_name/nmap/allPorts" ]
        then
            echo "· Ejecutando reconocimiento especifico..."
            echo
            Mensajes 3
            ExtraerPuerto
            nmap -sCV -p $ports $ip -oN /tmp/autoh4x/$proyecto_name/nmap/targeted
            echo
        else
            echo
            echo "· Ejecutando reconocimiento inicial..."
            echo
            nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn $ip -oG /tmp/autoh4x/$proyecto_name/nmap/allPorts
            echo
            echo "· Ejecutando reconocimiento especifico..."
            echo
            ExtraerPuerto
            nmap -sCV -p$ports $ip -oN /tmp/autoh4x/$proyecto_name/nmap/targeted
            echo
        fi
        Mensajes 5
        read
        menu_nmap
        ;;
    5)
        if [ -f "/tmp/autoh4x/$proyecto_name/nmap/allPorts" ]
        then
            echo ===============================================
            echo "· Printando resultados opción '3) Reconocimiento inicial'"
            echo
            ExtraerPuerto
            echo "· Los puertos abiertos son: $ports"
            echo ===============================================
        else
            echo 
            echo "· Reconocimiento inicial no realizado, fichero inexistente /tmp/autoh4x/$proyecto_name/nmap/allPorts"
            echo
        fi
            if [ -f "/tmp/autoh4x/$proyecto_name/nmap/targeted" ]
            then
                echo ===============================================
                echo "· Printando resultados opción '4) Reconocimiento especifico'"
                echo
                cat /tmp/autoh4x/$proyecto_name/nmap/targeted
                echo
                echo ===============================================
            else
            :
            fi
        Mensajes 5
        read
        menu_nmap
        ;;
    
    6)
            Mensajes 0
            ip=$(LeerIP)
            menu_nmap
            ;;

    7)
            menu_principal
            ;;
    8)
        exit
        ;;
    *)
        echo "· Opción no válida."
        Mensajes 5
        read
        menu_nmap
        ;;
    esac
}

menu_principal(){
if [ $# -eq 0 ]
then
    salida=0
    while [ $salida -ne 8 ] 
    do
        clear
        echo
        echo "==============="
        echo "Menu principal"
        echo "==============="
        echo
        echo "1) metasploit"
        echo "2) dirbuster"
        echo "3) nmap"
        echo "4) Proveer de Shell con Netcat"
        echo "5) Conectar a Shell remota con Netcat"
        echo "6) Cambiar IP"
        echo "7) Nuevo proyecto"
        echo "8) Borrar este proyecto"
        echo "9) Borrar todos los proyectos"
        echo "10) Salir"
        echo
        echo "IP objetivo es: $ip"
        echo "Nombre de Proyecto: $proyecto_name"
        echo
        echo -n "Seleccione una opción: "
        read opcion

    case $opcion in
        1)
            msfconsole
            ;;
        2) 
            dirbuster
            ;;
        3)
            menu_nmap
            ;;
        4)
            echo
            echo "· Indica puerto para proveer Shell remota con Netcat..."
            echo
            Mensajes 2
            puerto=$(LeerPuerto)
            nc -lvp $puerto
            ;;
        5)
            echo
            echo "· Indica Puerto para conectar a Shell remota con Netcat"
            echo
            Mensajes 2
            puerto=$(LeerPuerto)
            nc $ip $puerto
            ;;
        6)
            Mensajes 0
            ip=$(LeerIP)
            ;;
        7)
            proyecto
            ;;
        8)
            echo
            echo "¡Atención! Esta acción borrará este proyecto ($proyecto_name) y no podrás recuperarlo."
            read -p "¿Está seguro de continuar? (si/no): " loco
            if [ -n "$loco" ] && [[ "$loco" =~ ^[Ss][Ii]$ ]]
            then
                echo
                echo "· Has elegido borrar todas las carpetas de este proyecto"
                echo
                rm -rf /tmp/autoh4x/$proyecto_name
                echo "· Volviendo al menu proyecto..."
                echo
                echo "============================="
                Mensajes 5
                read
                proyecto
            else
                echo
                echo "· No has escrito 'si', nada se borrará. Volviendo al menu principal..."
                echo
                Mensajes 5
                read
                menu_principal
            fi
            ;;
        9) 
            echo
            echo "¡Atención! Esta acción borrará todos los proyectos y no podrás recuperarlos."
            read -p "¿Estas seguro?. Escribe 'Si' + Intro para continuar. Cualquier letra para salir: " loco
            if [ -n "$loco" ] && [[ "$loco" =~ ^[Ss][Ii]$ ]]
            then
                echo
                echo "· Has elegido borrar todas las carpetas de todos los proyectos ¯\_(ツ)_/¯ "
                echo
                rm -rf /tmp/autoh4x
                echo "· Volviendo al menu proyecto..."
                echo
                echo "============================="
                Mensajes 5
                read
                proyecto
            else
                echo
                echo "· No has escrito 'si', nada se borrará. Volviendo al menu principal..."
                echo
                Mensajes 5
                read
                menu_principal
            fi
            ;;
        10)
            Mensajes 4
            exit
            ;;
        *)
            echo
            echo "· Opción no válida."
            Mensajes 5
            read
            ;;
    esac
    done
else    
            echo "· Uso: $0"
            exit
fi
}

proyecto
menu_principal
```