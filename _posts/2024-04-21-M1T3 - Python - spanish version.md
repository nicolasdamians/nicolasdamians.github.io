---
layout: post
title: "M1T3 - Python Script - Spanish Version"
date: 2024-04-21 15:25:00 
categories:
    - posts
tags:
    - Master
    - Home_work
    - M1
    - script
    - python
    - es
---

## M1.Tarea 3. Python Script 

### Professional Master in Offensive Security (OSCP) (26/03/2024 - 31/03/2025)

- **Student:** Nicolas Sadofschi | gen0ne | [https://xargs.cat](https://xargs.cat)

# Enunciado de la tarea

En esta práctica debes realizar un script de Python que realice lo siguiente:

- Al ejecutar el script, éste debe recoger información del usuario que ejecuta el script, por ejemplo: el username
- Además, el script debe recopilar el path dónde se ejecuta el script.
- Además, el script debe listar el contenido de un directorio que es pasado como argumento.

El script debe devolver los resultados en una variable de tipo diccionario (dict).

Ejemplo:
Podemos ejecutar el script de la siguiente manera: ./script.py ruta_directorio

El script devolverá una variable dict con la siguiente información:

{'username' : nombre_usuario , 'path' : ruta_ejecución_script , 'directorio' : [fichero1, fichero2, fichero3, ..., ficheroN]}

Fijaros que el listado del directorio se devuelve en formato lista.

# Parte 2
```python
# Autor: Nicolás Damián Sadofschi | gen0ne | xargs.cat
# Fecha: 21/04/2024
# Versión: 1.0
# Master: Master Profesional en Seguridad Ofensiva (OSCP)
# Módulo: M1
# Tarea: Tarea 3 - Python

import os
import sys

## Inicio función obtener_informacion.
def obtener_informacion():
    # Obtener el nombre de usuario
    nombre_usuario = os.getlogin()

    # Obtener la ruta de ejecución del script
    path = os.getcwd()

    # Obtener la carpeta pasada como argumento
    if len(sys.argv) == 2:
        carpeta = sys.argv[1]
    else:
        print("Error: Proporciona una carpeta; Uso ./script.py < carpeta >; ejemplo: ./script.py c:\\users")
        sys.exit(1)

    # Listar el contenido de la carpeta. Flow: Primero creamos la lista contenido_carpeta vacia. Luego verificamos si la carpeta 'carpeta' existe y finalmente decimos que contenido_carpeta = os.listdir 'carpeta'.
    contenido_carpeta = []
    if os.path.exists(carpeta):
        contenido_carpeta = os.listdir(carpeta)
    else:
        print("Error: La carpeta '{0}' no existe.".format(carpeta))
        sys.exit(1)

    # Diccionario (clave - valor)
    informacion = {
        "username": nombre_usuario,
        "path": path,
        "carpeta": contenido_carpeta
    }

    return informacion

## Fin función obtener_informacion.

print(obtener_informacion())
```