---
title: WireGuard VPN
date: 2023-03-01 12:00:00 +0800
categories: [Redes y Telecomunicaciones, WireGuard]
tags: [WireGuard, VPN]
---

![VPN](https://restoreprivacy.com/wp-content/uploads/2022/11/WireGuard-VPN-2022.jpeg)

WireGuard es un protocolo de VPN de última generación que utiliza tecnología de cifrado avanzada y un enfoque más simple y eficiente que otros protocolos VPN, como OpenVPN o IPSec.

WireGuard se basa en una técnica criptográfica conocida como Curvas Elípticas de Difusión de Clave (Curve25519), que proporciona una seguridad fuerte con una sobrecarga de procesamiento mínima. Además, utiliza un modelo de túnel de red más eficiente que permite una conexión rápida y estable con una latencia muy baja.

WireGuard es compatible con muchos sistemas operativos, incluyendo Linux, Windows, macOS, Android e iOS. También tiene una interfaz de línea de comandos para una configuración más avanzada.

En resumen, WireGuard es una solución de VPN de alto rendimiento, segura y fácil de configurar, lo que la hace atractiva para los usuarios que buscan una solución VPN de última generación.

## Esquema de RED

![RED](https://user-images.githubusercontent.com/67869168/222277880-d1e008bb-541f-408d-8639-c9d0a5c04db2.svg)

WireGuard se basa en una técnica criptográfica conocida como Curvas Elípticas de Difusión de Clave (Curve25519), que proporciona una seguridad fuerte con una sobrecarga de procesamiento mínima. Además, utiliza un modelo de túnel de red más eficiente que permite una conexión rápida y estable con una latencia muy baja.

WireGuard se basa en una técnica criptográfica conocida como Curvas Elípticas de Difusión de Clave (Curve25519), que proporciona una seguridad fuerte con una sobrecarga de procesamiento mínima. Además, utiliza un modelo de túnel de red más eficiente que permite una conexión rápida y estable con una latencia muy baja.

WireGuard se basa en una técnica criptográfica conocida como Curvas Elípticas de Difusión de Clave (Curve25519), que proporciona una seguridad fuerte con una sobrecarga de procesamiento mínima. Además, utiliza un modelo de túnel de red más eficiente que permite una conexión rápida y estable con una latencia muy baja.


## Configuración de WireGuard en Ubuntu Server

### Actualizar el Sistema Operativo

```bash
sudo apt update && sudo apt upgrade
```
### Instalación del servidor VPN WireGuard

```bash
sudo apt install wireguard
```

### Configuración del servidor WireGuard

**Primer paso** que deveremos realizar es otorgarle permisos al directirio de wireguard (`/etc/wireguard/`{: .filepath}.), los permisos los permisos **077** lo que significa que el directorio tiene permisos totales de lectura, escritura y ejecución para el propietario, el grupo y otros usuarios.

```bash
sudo chmod 077 /etc/wireguard/
```

**Segundo paso** que deveremos realizar sera la creacion de las claves publicas y privadas, estas claves seran las del servidor y nos serviran para poder configurar las tarjetas de red virtuales que posterior mente configuraremos y para que los clientes no puedan identificar. El nombre que le demos da igual, no tiene que ser el mismo:

```bash
umask 077; wg genkey | tee private_key_server01 | wg pubkey > public_key_server01
```

Despues comprovaremos los permisos donde tendremos que tener los siguientes valores (077):

```bash
-rw------ 1 root root 45 Mar 12:00 private_key_server01
-rw------ 1 root root 45 Mar 12:00 private_key_server01
```

*   **Tercer paso** que deveremos realizar es la configuracion de la tarjeta de red virtual, estas son interfaces de red lógicas en un sistema operativo que no están asociadas directamente con hardware físico. En lugar de eso, se utilizan para proporcionar una comunicación lógica entre los componentes de software de un sistema operativo, como aplicaciones, controladores y protocolos de red.

Para realizar este paso crearemos el archivo `wg0.conf`:

```bash
sudo nano /etc/wireguard/wg0.conf
```

```conf
[Interface]
Address = 10.0.0.1/16
ListenPort = 5000
PrivateKey = *************************
```
{: file='/etc/wireguard/wg0.conf'}

Como ya he dicho antes el nombre no tiene porque ser el mismo, al mismo tiempo que la direccion de red y el purto tambien pueden ser los que queramos, 

> **Utilice una red privada**, se recomienda que se utilice una red privada para las direcciones IP de WireGuard. Las redes privadas son rangos de direcciones IP que se reservan para redes locales y no están enrutadas a través de Internet público. Los rangos de direcciones IP privadas son los siguientes:
```bash
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```
{: .prompt-info }


## Configuration del FireWall

## Configuración del Cliente VPN de WireGuar

El procedimiento para instalar y configurar un cliente VPN es el mismo que para configurar el servidor. 

*   **Instalacion de WireGuard**
*   **Asignacion de Permisos**
*   **Creacion de la clave Publica y Privada**

Una vez completados los anteirores pasos, quedara la configuración y conexion de la Tarjeta de Red de un equipo cliente

```bash
sudo nano /etc/wireguard/wg0.conf
```

```conf
[Interface]
PrivateKey = *************************
Address = 10.0.0.2/16
 
[Peer]
PublicKey = *************************
AllowedIPs = 16.0.0.0/16
Endpoint = 192.168.1.100:5000
PersistentKeepalive = 15
```
{: file='/etc/wireguard/wg0.conf'}

*   **Interface**: Esto define la interfaz de red de WireGuard en el cliente.
*   **PrivateKey**: Esta es la clave privada del cliente. Es importante mantener esta clave privada segura ya que se utiliza para autenticar la conexión del cliente con el servidor.
*   **Address**: Esta es la dirección IP asignada al cliente dentro de la red de WireGuard. En este caso, el cliente tendrá la dirección IP 10.0.0.2 con una máscara de subred /16 (que significa que la subred es 10.0.0.0/16).
*   **[Peer]**: Esta sección define la configuración del par de claves público/privada del servidor con el que se conectará el cliente.
*   **PublicKey**: Esta es la clave pública del servidor. El cliente utiliza la clave pública del servidor para autenticar la conexión del servidor.
*   **AllowedIPs**: Esto define los rangos de direcciones IP que el cliente enruta a través de la conexión de VPN. En este caso, el cliente enrutará todo el tráfico de la subred 10.0.0.0/16 a través de la conexión VPN.
*   **Endpoint**: Esta es la dirección IP y el puerto del servidor WireGuard al que se conectará el cliente. En este caso, el servidor está en la dirección IP 192.168.1.100 y el puerto de escucha es 5000.
*   **PersistentKeepalive**: Esta opción define un ping persistente que se envía al servidor para asegurarse de que la conexión se mantenga viva. En este caso, se enviará un ping cada 15 segundos.

Por parte del cliente esta seria toda la configuracion necesaria, ahora el siguiente paso sera reiniciar y habilitar la tarjeta de red virtual:

```bash
sudo systemctl enable wg-quick@wg0
```
```bash
sudo systemctl start wg-quick@wg0
```
```bash
sudo systemctl status wg-quick@wg0
```

## Anadir clientes al servidor

Necesitamos configurar la opción VPN peer-to-peer del lado del servidor y permitir una conexión entre la computadora cliente y el servidor. Volvamos a nuestro servidor  y editemos el archivo `wg0.conf` para agregar información `[Peer]` (cliente) de la siguiente manera:

```conf
[Peer]
PublicKey = *************************
AllowedIPs = 10.0.0.2/32
```
{: file='/etc/wireguard/wg0.conf'}

En mi caso practico necesito agregar a varios clientes por lo que tendremos que anadir a `wg0.conf` el resto de clientes:

```conf
[Peer]
PublicKey = *************************
AllowedIPs = 10.0.0.2/32

[Peer]
PublicKey = *************************
AllowedIPs = 10.0.0.3/32

[Peer]
PublicKey = *************************
AllowedIPs = 10.0.0.4/32
```
{: file='/etc/wireguard/wg0.conf'}

### Por que /32?

En la configuración del servidor WireGuard, se utiliza "/32" en la opción AllowedIPs para permitir al cliente enrutar el tráfico de una única dirección IP a través de la conexión VPN.

El número después de la barra diagonal (/) en una dirección IP indica el número de bits que se utilizan para representar la parte de la red y la parte de host de la dirección IP. En una dirección IP con una máscara de subred /32, todos los 32 bits se utilizan para representar la parte del host de la dirección IP, lo que significa que solo se permite enrutar el tráfico de una única dirección IP.

Por lo tanto, si desea permitir que un cliente enrute todo el tráfico de una subred a través de la conexión VPN, se debe utilizar una máscara de subred que cubra toda la subred, por ejemplo, "/24" para una subred de clase C (como 192.168.1.0/24), que permitiría enrutamiento para todas las direcciones IP de la subred.

## Habilitar y iniciar el servido de WireGuard

Como ultimos paso solo quedara  reiniciar y habilitar la tarjeta de red virtual:

```bash
sudo systemctl enable wg-quick@wg0
```
```bash
sudo systemctl start wg-quick@wg0
```
```bash
sudo systemctl status wg-quick@wg0
```
### Verificación

Muy simple lo unico que tendremos que hacer es un ping a cada uno de los terminales de la red:

```bash
ping 10.0.0.1
```

### Configuracion de WireGuard en Windows 10

### Configuracion de WireGuard en Android
