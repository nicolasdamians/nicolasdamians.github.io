---
layout: post
title: "M3T2 - Fake Images - Spanish Version"
date: 2024-05-15 10:25:00 
categories:
    - posts
tags:
    - Master
    - osint
    - tools
    - es
---

### 

ÍNDICE DE CONTENIDOS

[**Enunciado de la tarea 3**](#enunciado-de-la-tarea)

[**Pasos de la investigación 3**](#pasos-de-la-investigación)

> [0- Descarga de la imagen 4](#descarga-de-la-imagen)
>
> [1- Obtención de Metadatos 4](#obtención-de-metadatos)
>
> [2- Búsqueda de fotos similares 4](#búsqueda-de-fotos-similares)
>
> [3- Descargamos imagen de Twitter y obtenemos metadatos
> 6](#descargamos-imagen-de-twitter-y-obtenemos-metadatos)
>
> [4- Análisis mediante ELA 7](#análisis-mediante-ela)

[**Conclusiones 9**](#conclusiones)

# 

# 

# 

# 

# 

# 

# 

# 

# 

# 

# 

# 

# 

# 

# 

# 

# 

# Enunciado de la tarea

El día 30 de octubre el conseller de la Generalitat Josep Rull se
presentó en su despacho de la Generalitat de Cataluña y se tomó una
famosa imagen trabajando.

El objetivo de la tarea es que nos digas si la imagen es original o ha
sido modificada, siguiendo la metodología descrita en el manual y en la
tutoría.

Si consideras que ha sido modificada, nos tienes que decir qué elementos
han sido manipulados.

La imagen la puedes descargar desde:
[[https://pbs.twimg.com/media/DNZWRSsWsAAMMw5.jpg:large]{.underline}](https://pbs.twimg.com/media/DNZWRSsWsAAMMw5.jpg:large)

# 

# Pasos de la investigación

0- Descarga de la Imagen facilitada.\
1- Extracción de metadatos.\
2- Búsqueda de fotos similares.\
3- Coincidencias encontradas, extracción de metadatos.\
4- Análisis ELA.\
5- Conclusiones.

## 

## 

## 

## 

## 

## 

## 0- Descarga de la imagen

## ![](media/image4.png){width="6.5in" height="2.1944444444444446in"}

## 1- Obtención de Metadatos

1.1- Usando '[[exiftool]{.underline}](https://exiftool.org/)' obtenemos
la fecha de modificación (2017:10:30 16:26:36)
![](media/image2.png){width="6.375in" height="2.1145833333333335in"}

## 2- Búsqueda de fotos similares

2.1- Haciendo uso de Microsoft Edge: Clic Derecho sobre la imagen -\>
Búsqueda Visual

2.2 - Se encuentran resultados, potencialmente, interesantes.

![](media/image7.png){width="6.436196412948381in"
height="3.4079866579177605in"}

2.3 - El primer enlace nos redirige a la página web de 'El Periódico'
enlace: [[Independencia de Cataluña: Josep Rull acude a su despacho
en]{.underline} [Territori]{.underline}
[(elperiodico.com)]{.underline}](https://www.elperiodico.com/es/politica/20171030/rull-acude-despacho-frente-cese-6389626)

2.4- Dentro de la web de El Periodico vemos dos imágenes:\
Imagen A:\
![](media/image9.png){width="4.486963035870517in"
height="3.216146106736658in"}\
[[Imagen
B]{.underline}](https://twitter.com/joseprull/status/924912753009143808?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E924912753009143808%7Ctwgr%5E69d4a86899ff5e1571b7de8364ae4c531115632d%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fwww.elperiodico.com%2Fes%2Fpolitica%2F20171030%2Frull-acude-despacho-frente-cese-6389626)
(Twitter):
https://twitter.com/joseprull/status/924912753009143808/photo/1\
[![](media/image5.png){width="5.411841644794401in"
height="6.347222222222222in"}](https://twitter.com/joseprull/status/924912753009143808/photo/1)

## 3- Descargamos imagen de Twitter y obtenemos metadatos

3.1 - Clic derecho sobre la imagen, y seleccionamos 'Abrir imagen en
pestaña nueva'.\
3.2 - Obtenemos URL de la nueva imagen. ( [[DNXzPYhX0AAtMyL (680×510)
(twimg.com)]{.underline}](https://pbs.twimg.com/media/DNXzPYhX0AAtMyL?format=jpg&name=small)
) y descargamos mediante wget en un terminal linux.\
3.3 - Obtenemos Metadatos:\
![](media/image3.png){width="6.5in" height="4.75in"}

## 4- Análisis mediante ELA

Accedemos a Fotoforensics.com y pegamos la URL de la Imagen facilitada
para el análisis.

[[FotoForensics -
Analysis]{.underline}](https://fotoforensics.com/analysis.php?id=ace62ed2816bf46f60f02f0aacc3a242f406708f.121078)

![](media/image6.png){width="6.201388888888889in"
height="9.235457130358705in"}

# Conclusiones

-   **Fecha** de la
    [[imagen]{.underline}](https://pbs.twimg.com/media/DNZWRSsWsAAMMw5.jpg:large)
    facilitada para la auditoría: Se concluye que la fecha de creación
    es posterior a la [[Imagen
    B]{.underline}](https://twitter.com/joseprull/status/924912753009143808?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E924912753009143808%7Ctwgr%5E69d4a86899ff5e1571b7de8364ae4c531115632d%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fwww.elperiodico.com%2Fes%2Fpolitica%2F20171030%2Frull-acude-despacho-frente-cese-6389626)
    (Twitter). Evidencias en los puntos
    [[1.1]{.underline}](#obtención-de-metadatos),
    [[1.2]{.underline}](#obtención-de-metadatos) y
    [[2.4]{.underline}](#búsqueda-de-fotos-similares) **Imagen B** y
    [[3.3]{.underline}](#descargamos-imagen-de-twitter-y-obtenemos-metadatos).\
    Explicación: En los puntos
    [[1.1]{.underline}](#obtención-de-metadatos) y
    [[1.2]{.underline}](#obtención-de-metadatos), extraemos los
    Metadatos de la imagen facilitada para análisis, estos indican que
    la imagen fue modificada el 30 de Octubre 2017 a las 16:26.\
    En los puntos [[2.4]{.underline}](#búsqueda-de-fotos-similares)
    **Imagen B** y
    [[3.3]{.underline}](#descargamos-imagen-de-twitter-y-obtenemos-metadatos)
    se evidencia que el Post realizado por la persona ([[Josep Rull i
    Andreu]{.underline}](https://twitter.com/joseprull)) protagonista de
    la foto es del 30 de Octubre 2017 a las 09:15 a.m.

-   **ELA (Error Level Analysis):** El análisis ELA ([[punto
    4]{.underline}](#análisis-mediante-ela)) demuestra una manipulación
    sobre los Mapas (en formato Cuadro) colgados detrás de 'Josep Rull i
    Andreu'.

  -------------------------------------------------------------------------------------------------------------------
  Izquierda                                                                       Derecha: Imagen Original obtenida
  [[Imagen]{.underline}](https://pbs.twimg.com/media/DNZWRSsWsAAMMw5.jpg:large)   mediante búsqueda
  facilitada para análisis                                                        
  ------------------------------------------------------------------------------- -----------------------------------

  -------------------------------------------------------------------------------------------------------------------

![](media/image8.png){width="6.5in" height="3.125in"}
