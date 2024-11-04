---
layout: post
title: "M3T4 - Hidden Services - Spanish Version"
date: 2024-05-15 10:25:00 
categories:
    - posts
tags:
    - Master
    - es
---

# Crear el contenido en formato Markdown
markdown_content = """
# ÍNDICE DE CONTENIDOS

- [Enunciado de la tarea](#enunciado-de-la-tarea)
- [Despliegue de Stack ELK](#despliegue-de-stack-elk)
- [Proceso de instalación de ELK + Kibana](#proceso-de-instalación-de-elk--kibana)
  - [Elastic](#elastic)
  - [Kibana](#kibana)
  - [Logstash](#logstash)
- [Input](#input)
- [Filtros](#filtros)
- [Output](#output)
- [Configuración del servicio logstash](#configuración-del-servicio-logstash)
- [Dashboards Kibana](#dashboards-kibana)

## Enunciado de la tarea

ELK se puede utilizar de diferentes formas:
- Instalando las aplicaciones descargadas desde el repositorio de elastic.co
- Utilizando docker-compose para levantar los servicios (https://www.docker.elastic.co/)
- Utilizando una instancia de Elastic Cloud
- Simplemente descargando las aplicaciones y ejecutándose sin necesidad de instalarlas

Esta actividad tiene el objetivo de interiorizar el funcionamiento de la pila ELK monitorizando logs del sistema.

La tarea consiste y tiene un valor de:
- Despliegue de ELK (10%)
- Descripción del formato de los logs elegidos indicando con un ejemplo la estructura de una línea modelo (20 %)
- Configuración de Logstash o beats utilizados (30%)
- A partir de los datos ingestados mostrar un dashboard de Kibana que contenga al menos cuatro visualizaciones. Se proponen algunas de las siguientes visualizaciones (40%):
  - Número total de eventos.
  - Ficheros más habituales
  - Categorías más recurrentes
  - Etc.

En esta actividad se deberán usar al menos el input plugin `file{}`, el filter plugin `grok{}` y el output plugin de `elasticsearch{}`.

## Despliegue de Stack ELK

El enfoque inicial que planteé fue desplegar y usar el Elastic Cloud cosa que me pareció extremadamente sencillo. Finalmente opté por desplegar la siguiente infraestructura en mi lab on prem:

La idea es dejar una VM con Logstash y otra con ELK + Kibana. Habrá dos dispositivos físicos enviando mediante Syslog datos a Logstash, este los enviará a ELK y Kibana los indexará y mostrará. Nota: Para este ejercicio solamente exportaré logs desde el Fortigate.

## Proceso de instalación de ELK + Kibana

https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html

https://www.elastic.co/guide/en/kibana/current/deb.html

### Elastic

Mediante `apt` instalamos elastic previo a añadir su PGP y repositorio tal como indica la guía adjunta.

Configuramos el daemon para que arranque con el OS:

Comprobamos que esté levantado:

Configuramos de manera muy básica ELK:

(cambios realizados son las líneas ‘<’)

### Kibana

Instalamos mediante `apt`.

Configuramos demonio, levantamos y hacemos backup de la config:

Copiamos el cert CA de Elastic a Kibana:

Reseteamos pass de Kibana:

Realizamos configuración básica de Kibana:

Reseteamos el servicio de Kibana y comprobamos que levante correctamente.

### Logstash

Ref: https://www.elastic.co/guide/en/logstash/current/installing-logstash.html

Actualizar lista de paquetes y actualizarlos.

Instalar `openjdk-8-jre`

\`\`\`bash
root@k8s-1:~# apt-get install openjdk-8-jre
\`\`\`

Instalar la Public Signing Key, agregar el repo e instalar Logstash mediante `apt`.

## Input

Creamos un archivo de configuración exclusivo para el Fortigate. Nos vamos a basar sobre UDP input Plugin y a sacar el output por consola.

Configuramos el syslog en el Forti:

Levantamos logstash con el archivo de config creado en el paso anterior para examinar los logs que nos va a pasar el Forti.

## Filtros

https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html

Copiamos un mensaje que contenga información del Forti para jugar con el Grok Debugger que podemos encontrar en Kibana.

Explicación:
- `%{SYSLOG5424PRI}`: Este es un patrón predefinido de Grok que corresponde a la prioridad de syslog en el formato RFC5424. Este formato incluye un número de prioridad encerrado entre `<` y `>`; en el ejemplo: `<189>`.
- `%{GREEDYDATA:message}`: Este es otro patrón predefinido de Grok. GREEDYDATA captura cualquier cantidad de caracteres hasta el final de la línea. `:message` indica que todo lo que se capture con GREEDYDATA se almacenará en el campo llamado `message`.

Borramos información que no interesa mediante `mutate`:

Finalmente tras varias horas de prueba y error y de obtener diferentes fuentes de información, el filtro queda así:

Usando `KV` borramos las contrabarras y agregamos un salto de línea.

Hecho una prueba con `logdate` y `date`, necesito más horas para desarrollar o debuggear.

También encuentro útil convertir a Integer los bytes enviados y recibidos.

## Output

https://www.elastic.co/guide/en/logstash/current/plugins-outputs-elasticsearch.html

Definimos el output filter de elasticsearch indicando el hosts, creando un index, especificando user y pass de elastic e indicamos que CA debe usar (la de Elastic).

## Configuración del servicio logstash

\`\`\`bash
root@k8s-1:~# systemctl start logstash.service
\`\`\`

También comentamos la línea del filtro de output `stdout` ya que no es necesario ver la salida por consola más.

## Dashboards Kibana

Creamos un data view:

Una vez creado el data view mediante `Analytics -> Discover`, es cuestión de ir buscando la combinación de datos que resulte más interesante para crear dashboards. Ejemplo:

---

Máster en Seguridad Ofensiva (OSCP)

M3. T3 - Monitorización LOGS con ELK

Nicolás Damián Sadofschi | https://xargs.cat
Página |
"""

