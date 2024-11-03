---
layout: post
title: "aguademayo  - dockerlabs"
date: 2024-10-29 06:25:00 -0400
categories:
    - posts
tags:
    - Road2OSCP
    - CTF
    - en
---
# aguademayo [dockerlabs]

- enumeramos como siempre, vemos ssh y http
- dirbuster y vamos a /images unico directorio
- hay una imagen, la bajamos
- intetnamos con steghide sacar informacion pero tiene pass y no podemos
- intentamos hacer brute force pero no podemos romperla
- exploramos el codigo fuente del index que parece por default, al final vemos:
- 

```bash
++++++++++[>++++++++++>++++++++++>++++++++++>++++++++++>++++++++++>++++++++++>++++++++++++>++++++++++>+++++++++++>++++++++++++>++++++++++>++++++++++++>++++++++++>+++++++++++>+++++++++++>+>+<<<<<<<<<<<<<<<<<-]>--.>+.>--.>+.>---.>+++.>---.>---.>+++.>---.>+..>-----..>---.>.>+.>+++.>.

```

- usamos [https://www.splitbrain.org/_static/ook/](https://www.splitbrain.org/_static/ook/) previo busqueda en google y nos da como resultaod
- bebeaguaqueessano
- intentamos ssh como root y esa pass y nada
- intentamos steghide extract con esa pass y nada
- como el archivo q bajamos se llama “agua_ssh” intentamos con usuario agua y pass la extraida ‘bebeaguaqueessano’
- 

# escalada


![alt text](<../attachments/aguademayo/image 1.png>)![alt text](../attachments/aguademayo/image.png)
_______________

# Report

## Enumeration

```bash
└─#  nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn  $target -oG allPorts

```

![alt text](<../attachments/aguademayo/image 2.png>)

Found open ports 22 and 80

### web enumeration
![alt text](<../attachments/aguademayo/image 3.png>)![alt text](<../attachments/aguademayo/image 4.png>) ![alt text](<../attachments/aguademayo/image 5.png>)

Downloaded image and tried to bruteforce it

made curl and saw this at the bottom:

![image.png](image%206.png)
![alt text](<../attachments/aguademayo/image 6.png>)
```bash
 ~  curl -vvv http://172.17.0.2                                                                                                                                                                                                                                                                                                                           ✔  06:24:51 

```

used [https://www.splitbrain.org/_static/ook/](https://www.splitbrain.org/_static/ook/) previous google search to understand wtf was that

result was

![image.png](image%207.png)
![alt text](<../attachments/aguademayo/image 7.png>)
tried ssh with that pass and user root, without luck

tried extract image with that idem

finally ssh was with user ‘agua’ since agua was part of the name of the image

once ssh’ed ran sudo -l

![image.png](image.png)![alt text](../attachments/aguademayo/image.png)

![image.png](image%201.png)
![alt text](<../attachments/aguademayo/image 1.png>)
found better cap and saw that could run shell commands

sent a reverse shell and finish