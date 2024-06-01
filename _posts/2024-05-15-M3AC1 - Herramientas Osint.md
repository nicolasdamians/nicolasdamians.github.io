---
layout: post
title: "M3AC1 - Herramientas Osint - Spanish Version"
date: 2024-05-15 10:25:00 
categories:
    - posts
tags:
    - Master
    - Home_work
    - M3
    - osint
    - tools
    - es
---
# Crear el contenido en formato Markdown
markdown_content_osint = """
# ÍNDICE DE CONTENIDOS

- [Enunciado de la tarea](#enunciado-de-la-tarea)
- [theHarvester](#theharvester)
- [Wappalyzer](#wappalyzer)
- [SpiderFoot](#spiderfoot)

## Enunciado de la tarea

A través de esta actividad queremos generar un listado de herramientas y aplicaciones que nos pueden ser útiles en el reconocimiento perimetral de una organización. El objetivo es generar un listado de herramientas que puedan ser utilizadas para ayudar a trazar la huella digital de una ciberidentidad o al procesamiento de información de fuentes abiertas.

Las herramientas deberán tener en cuenta sobre qué inputs de información trabaja la identidad. Un ejemplo podría ser:
- Nombres de usuario
- Imágenes
- Datos cartográficos
- Dominios
- Números de teléfono
- …

Junto con cada herramienta habrá que incluir una breve ficha de en qué escenarios nos va a ayudar esa herramienta así como la información de partida desde la que empieza. Os proponemos la siguiente estructura para vuestra aportación:
- Nombre la herramienta:
- URL:
- Datos de entrada:
- Funcionalidad:
- Instrucciones de instalación (si procede):
- Ejemplo de uso:

El resultado de la actividad será una guía de herramientas de obtención de información de fuentes abiertas elaborada de forma colaborativa a través de vuestras aportaciones.

## theHarvester

**Nombre de la herramienta:** theHarvester  
**URL:** [theHarvester](https://github.com/laramies/theHarvester)  
**Datos de entrada:** 
- Nombres de dominio
- Direcciones de correo electrónico
- Nombres de empleados

**Funcionalidad:**  
theHarvester es una herramienta de recopilación de información que busca datos públicos en diferentes fuentes como motores de búsqueda, redes sociales y bases de datos públicas. Es útil para obtener direcciones de correo electrónico, subdominios, nombres de empleados y direcciones IP relacionadas con un dominio. Esto es esencial para realizar un reconocimiento pasivo de una organización y entender mejor su exposición en línea.

**Instrucciones de instalación:**  
Para instalar theHarvester necesitas tener Python instalado en tu sistema. Luego clona el repositorio desde GitHub y sigue las instrucciones para instalar las dependencias necesarias. Puedes ejecutar theHarvester desde la línea de comandos. Las API Keys se configuran en: `/etc/theHarvester/api-keys.yaml`. theHarvester ya viene en distribuciones como Kali Linux.

**Ejemplo de uso:**  
Supongamos que estás investigando la huella digital de una empresa específica. Usas theHarvester para buscar su dominio principal y la herramienta te proporciona una lista de direcciones de correo electrónico asociadas, subdominios y posibles direcciones IP. Esta información puede usarse para identificar posibles puntos de acceso y áreas de vulnerabilidad.

## Wappalyzer

**Nombre de la herramienta:** Wappalyzer  
**URL:** [Wappalyzer](https://www.wappalyzer.com)  
**Datos de entrada:**
- Encabezados HTTP
- Scripts JavaScript
- Patrones de archivos

**Funcionalidad:**  
Wappalyzer identifica las tecnologías que utilizan los sitios web. Proporciona información sobre servidores, sistemas de gestión de contenido (CMS), herramientas de análisis, bibliotecas JavaScript, frameworks, entre otros. Es útil para desarrolladores, profesionales de marketing y analistas de seguridad para entender la infraestructura de un sitio web, realizar auditorías tecnológicas y mejorar estrategias digitales basadas en las herramientas detectadas.

**Instrucciones de instalación:**  
Para instalar Wappalyzer visita su sitio web oficial y descarga la extensión para tu navegador (Chrome, Firefox, etc.). Sigue las instrucciones para añadirla. Una vez instalada, Wappalyzer identificará automáticamente las tecnologías usadas en los sitios web que visitas.

**Ejemplo de uso:**  
En el análisis de una web para una auditoría, con la ayuda de esta herramienta distinguir rápidamente si dicha web es un CMS tipo Joomla, WordPress, etc. Así mismo, también puedes comprobar rápidamente las librerías que usa, por ejemplo, jQuery.

## SpiderFoot

**Nombre de la herramienta:** SpiderFoot  
**URL:** [SpiderFoot](https://www.spiderfoot.net)  
**Datos de entrada:**  
Se trata de un programa de código abierto especializado en recolección de información para ciberseguridad. Acepta variables como nombres de dominio, direcciones IP, direcciones de correo electrónico, nombres de usuarios y más, ayudando en análisis de amenazas y recopilación de inteligencia.

**Funcionalidad:**  
SpiderFoot es un software utilizado para realizar inteligencia de fuentes abiertas (OSINT) y recopilación automatizada de datos sobre un objetivo como una dirección IP, nombre de dominio o dirección de correo electrónico. Ayuda a identificar vulnerabilidades, recolectar información sobre amenazas potenciales y mapear la exposición en línea de una entidad. Es valioso en seguridad cibernética porque permite a los profesionales obtener una visión integral del riesgo y tomar medidas preventivas para proteger sus activos y datos críticos.

**Instrucciones de instalación:**  
Para instalar SpiderFoot necesitas Python instalado. Luego descarga el repositorio desde GitHub, navega al directorio del proyecto y ejecuta `pip install -r requirements.txt`. Finalmente, inicia la aplicación con `python sf.py` o `python3 sf.py` para finalmente acceder desde un browser a tu `localhost:5001`.

\`\`\`bash
wget https://github.com/smicallef/spiderfoot/archive/v4.0.tar.gz
tar zxvf v4.0.tar.gz
cd spiderfoot-4.0
pip3 install -r requirements.txt
python3 ./sf.py -l 127.0.0.1:5001
\`\`\`

Se recomienda mediante la opción settings que el usuario añada tantas API keys como le sean posibles y las exporte de tal manera de poder usarlas en caso de que fuere necesario evitando que tenga que agregarlas manualmente otra vez.

**Ejemplo de uso:**  
Es una herramienta de código abierto para el reconocimiento de información mediante OSINT. Se emplea identificando objetivos y escaneando la red en busca de datos relacionados como direcciones IP, correos electrónicos, nombres de dominio y otros. Es útil en investigaciones de ciberseguridad y recopilación de inteligencia.

---

Máster en Seguridad Ofensiva (OSCP)

M3. AC1  - Herramientas OSINT

Nicolás Damián Sadofschi | [https://xargs.cat](https://xargs.cat)
"""

