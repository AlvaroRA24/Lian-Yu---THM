author: Alvaro Ramos Albertos
summary: Máquina TryHackMe - Lian_Yu
categories: codelab,markdown
environments: Web
status: Published

##Replanteo
Para empezar a trabajar solo se nos ocurre dos cosas:
- Escanear los puertos abiertos
- Sacar los directorios web de la dirección que nos da el ejercicio

##NMAP - Escaneo de puertos
Para el escaneo de puertos vamos a usar la herramienta NMAP. Debemos escribir el siguiente comando:
nmap -sV -sC (ip)
Donde:
- sC: es para script predeterminados
- sV: es para la detección de la versión

##Directorios web - GOBUSTER
Ahora vamos a ver si podemos sacar algún directorio web que nos ayude a resolver la máquina. Vamos a usar la herramienta gobuster escribiendo el siguiente comando:
gobuster dir -u -w
Donde:
- u: dirección, precedida por http:// (también tenemos que escribirlo)
- w: ubicación del diccionario que vamos a usar que en nuestro caso es /usr/share/
Como podemos ver, hemos encontrado el directorio /island
Al buscar la dirección ip con este directorio, encontramos el username, que está oculto: vigilante 

##GOBUSTER - Diccionario de números
De nuevo, vamos a usar la herramienta GOBUSTER, pero esta vez introduciremos la ip con el directorio encontrado y le aplicaremos un diccionario de números. para ello escribiremos:
gobuster dir -u (ip) -w /usr/share/seclists/Fuzzing/4-digits-0000-9999.txt
Encontramos el directorio /2100

##GOBUSTER - Directorio /2100
Usamos otra vez la herramienta gobuster, pero introducimos el siguiente comando:
gobuster dir -u (ip/island/2100) -w /usr/share/wordlist/dirbuster/directory-list-2.3-medium.txt -x ticket
Encontramos el nombre del archivo green_arrow.ticket

##Contraseña
Para sacar la contraseña del servicio FTP, abrimos navegador y escribimos ip/island/2100/green_arrow.ticket
A continuación tenemos que buscar cyberchef y copiar lo que hemos obtenido. 
Aplicamos la base 58 y obtenemos la contraseña

##FTP
Ahora vamos a entrar al servicio FTP, para ello escribimos en la terminal ftp y la ip
Nos pedirá usuario y contraseña (las que hemos obtenido anteriormente)
Una vez dentro, hacemos un ls -la para ver el contenido de los directorios del servicio. Vemos que hay otro usuario (.other_user). Usamos el comando mget * para transferirnos los archivos, y el comando "get .other_user" para obtener el usuario encontrado.
Nos salimos del servidor FTP y en la terminal hacemos "cat .other_user". Aquí vemos que el username del SSH es Slade.
A continuacion usamos el comando "nautilus ." que nos muestra los archivos obtenidos del servidor FTP, y vemos que no podemos acceder al archivo png llamado "leave_me_alone". 
Buscamos el codigo de los archivos png en el buscador y en la terminal lo escribimos tras escribir el comando "hexeditor LE"
Volvemos a escribir el comando "nautilus ." y vemos como ya nos deja abrir la foto, encontrando la contraseña del ssh

##SSH
Para entrar en el servidor ssh escribimos en la terminal lo siguiente: ssh sladre@(ip)
Nos pedirá la contraseña

##Enlace a las fotos
[CAPTURAS MÁQUINA]
(https://drive.google.com/drive/folders/1QAZpO8WqTF4aYQphLVW5dib6hkuxWMQp?usp=sharing)

- NO ME DA TIEMPO A EXPLICARLO TODO
