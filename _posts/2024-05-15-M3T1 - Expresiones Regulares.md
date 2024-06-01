### 

# ÍNDICE DE CONTENIDOS

[**Enunciado de la tarea 2**](#enunciado-de-la-tarea)

> [**Notas tutoria: 3**](#notas-tutoria)

[**¿Cómo hacemos la conversión? 3**](#cómo-hacemos-la-conversión)

[**¿Cómo hemos buscado la expresión regular?
3**](#cómo-hemos-buscado-la-expresión-regular)

[**Script 6**](#script)

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

# 

# 

# Enunciado de la tarea

El alumno debería descargar [[el siguiente PDF del
BOE]{.underline}](https://www.boe.es/boe/dias/2015/05/27/pdfs/BOE-A-2015-5834.pdf)
y, adjuntar en el entregable del ejercicio, un listado con todos los DNI
del documento, el número total de DNI encontrados, así como la expresión
regular utilizada para extraer este tipo de información del documento
PDF.

## Notas tutoria:

-   Explicar cómo hacemos la conversion

-   Como hemos buscado la expresión regular

# 

# ¿Cómo hacemos la conversión?

![](media/image6.png){width="4.354166666666667in" height="2.09375in"}

La función 'func_pdf_file' recibe un argumento de la función
'menu_principal', este argumento previa validación del mismo espera que
sea un archivo PDF.

Mediante 'pdftotext' lo convierte a txt. La sintaxis es: pdftotext
\<archivo pdf\> \<archivo salida txt\>

# Explicación RegEx '\\b\[0-9\]{7,8}\[\\-\\ \]?\[A-Za-z\]?\\b'

\\b = World Boundary o Frontera de Palabra. En este contexto sirve para
asegurarnos que encontramos números completos y no una parte de él.
Ejemplo: lorem 12345678A ipsum. En el contexto del script sirve para que
no encuentre falsos positivos, detectando NIES como DNIS. Ejemplo:
X6989644J.

\[0-9\]{7,8} = Match del 0 al 9, 7 u 8 veces.

\[\\-\\ \]? = Escapa el guión usando \\, evalua Guión o Espacio, el
interrogante indica que puede ser true o false (es decir opcional) tanto
el guión como el espacio.

\[A-Za-z\]? = 1 Letra de la A a la Z en Mayúsculas o Minúsculas,
opcional. En el contexto del script: 076923145

***NOTA***: También sería posible agregar a la expresión regular
condicionales para evaluar si hubieran puntos o espacio entre el grupo
de caracteres. Sin embargo para este ejercicio he decidido dejarla más
sencilla. Ejemplo:

![](media/image2.png){width="6.5in" height="2.8194444444444446in"}

# ¿Cómo hemos buscado la expresión regular?

Parto de la base de la regex proporcionada en la
[[guia]{.underline}](https://aula.campusciberseguridad.com/pluginfile.php?file=%2F41572%2Fmod_resource%2Fcontent%2F16%2FMANUAL%20INVESTIGACI%C3%93N%20FUENTES%20ABIERTAS.pdf)
Pag. 65 \[0-9\]{7,8}\[\\- \]?\[A-Z\].

A base de prueba y error mediante
[[https://regexr.com/]{.underline}](https://regexr.com/) y
[[https://regexper.com/#%5Cb%5B0-9%5D%7B7%2C8%7D%5B%5C-%5C%20%5D%3F%5BA-Z%20a-z%5D%3F%5Cb%0A]{.underline}](https://regexper.com/#%5Cb%5B0-9%5D%7B7%2C8%7D%5B%5C-%5C%20%5D%3F%5BA-Z%20a-z%5D%3F%5Cb%0A)

Término seleccionando: '\\b\[0-9\]{7,8}\[\\-\\ \]?\[A-Za-z\]?\\b'. La
razón principal es porque el script está diseñado con una expresión
regular para encontrar DNIs (\\b\[0-9\]{7,8}\[\\-\\ \]?\[A-Za-z\]?\\b) y
otra para NIEs (\[X-Zx-z\]\[0-9\]{7,8}\[A-Za-z\]\\b).

![](media/image3.png){width="3.12371062992126in"
height="2.376735564304462in"}

![](media/image7.png){width="6.5in" height="1.4166666666666667in"}

En un principio también había evaluado \\b\[0-9\]{7,8}\[\\-\\
\]?\[A-HJ-NP-TV-Za-hj-np-tv-z\]?\\b ya que en el [[DNI
Español]{.underline}](https://es.wikipedia.org/wiki/N%C3%BAmero_de_identidad_de_extranjero#Estructura)
'No se utilizan las letras: I, Ñ, O, U. La I y la O se descartan para
evitar confusiones con otros caracteres, como 1, l o 0. La Ñ se descarta
para evitar confusiones con la N.' No obstante, puse mucho esmero en
poder validar si los DNIs en los PDF's son correctos o no. Para hacer
esta validación no lo haré con las expresiones regulares sino con una
función específica.

Como dato curioso hay tres DNIS incorrectos. A uno le falta el dígito de
control y los otros dos hay typos .

![](media/image4.png){width="4.453125546806649in"
height="8.409786745406825in"}***\
Ejemplo con 16605390Z***

DNI NOK fuente el [[PDF del
Master]{.underline}](https://www.boe.es/boe/dias/2015/05/27/pdfs/BOE-A-2015-5834.pdf)
"LEIVA LÁZARO, FRANCISCO JAVIER. DNI 16605390Z."

![](media/image5.png){width="4.055215441819772in"
height="2.4600699912510935in"}\
DNI OK: "Leiva Lázaro, Francisco Javier 16605370Z " Fuentes:
[[Ej1]{.underline}](https://www.boe.es/boe/dias/2015/05/12/pdfs/BOE-A-2015-5254.pdf),
[[Ej2]{.underline}](https://www.boe.es/diario_boe/txt.php?id=BOE-A-2015-5254)

# 

# Script

Ver DNI_NIE-PDF.sh

#  Video PoC

[[https://www.youtube.com/watch?v=4H1y7zbnkes]{.underline}](https://www.youtube.com/watch?v=4H1y7zbnkes)

# Recursos:

[[https://es.wikipedia.org/wiki/N%C3%BAmero_de_identidad_de_extranjero#Estructura]{.underline}](https://es.wikipedia.org/wiki/N%C3%BAmero_de_identidad_de_extranjero#Estructura)\
[[https://www.validardni.es/]{.underline}](https://www.validardni.es/)

[[http://despropositosinformaticos.blogspot.com/2012/10/script-bash-validar-dni.html]{.underline}](http://despropositosinformaticos.blogspot.com/2012/10/script-bash-validar-dni.html)

[[https://www.interior.gob.es/opencms/ca/servicios-al-ciudadano/tramites-y-gestiones/dni/calculo-del-digito-de-control-del-nif-nie/]{.underline}](https://www.interior.gob.es/opencms/ca/servicios-al-ciudadano/tramites-y-gestiones/dni/calculo-del-digito-de-control-del-nif-nie/)
