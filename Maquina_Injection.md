# Máquina Injection
- Dificultad: muy fácil

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
* Esto **acelera el escaneo**, pero puede generar falsos positivos si la red/host no lo soporta bien.


`-n`

* Desactiva la **resolución DNS** (no intenta convertir IPs en nombres de dominio).
* Esto **acelera** el escaneo.


`-vvv`

* Modo **muy detallado** de salida (`-v` = verbose, `-vv` = más verboso, `-vvv` = máximo).
* Muestra más información en tiempo real del escaneo.


`-Pn`

* Le dice a Nmap que **no haga ping al host** antes de escanear.
* Asume que el host está **"up"**, útil si ICMP está bloqueado por firewall.


`-oN <ruta>`

* Guarda la salida del escaneo en **la ruta que hayamos seleccionado ** en mi caso:  /home/kali/Escritorio/dockerlabs_maquinas/maquinas_muy_faciles/maquina_injection_muyfacil/escaneo_maquina_inyection.
