# Proyecto de Red con Rutas Estáticas (Cisco Packet Tracer)

Este proyecto implementa una red básica de tres routers interconectados mediante enlaces seriales, con tres redes LAN independientes. El enrutamiento entre las redes se realiza mediante **rutas estáticas** configuradas manualmente
La red está diseñada para permitir comunicación entre tres redes locales (LAN), conectadas cada una a un router independiente. Los routers se enlazan entre sí mediante redes seriales punto a punto.  
El enrutamiento se realiza **sin protocolos dinámicos**, usando únicamente rutas estáticas.

<img width="669" height="436" alt="Pasted image 20251209142954" src="https://github.com/user-attachments/assets/385c15bd-200a-4959-a937-77f0fdf92901" />

Esquema de direccionamiento
Redes LAN

| Red   | Dirección   | Máscara       | Router Gateway |
| ----- | ----------- | ------------- | -------------- |
| LAN 1 | 192.168.1.0 | 255.255.255.0 | 192.168.1.1    |
| LAN 2 | 192.168.2.0 | 255.255.255.0 | 192.168.2.1    |
| LAN 3 | 192.168.3.0 | 255.255.255.0 | 192.168.3.1    |

Enlaces Seriales
| Enlace  | Dirección       | Máscara     | Dispositivos        |
| ------- | --------------- | ----------- | ------------------- |
| R1 ↔ R2 | 162.16.0.0 / 16 | 255.255.0.0 | R1: .0.1 / R2: .0.2 |
| R2 ↔ R3 | 162.17.0.0 / 16 | 255.255.0.0 | R2: .0.1 / R3: .0.2 |

## Configuración router 1

```bash
enable
configure terminal

interface fastEthernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

interface serial 0/1/0
ip address 162.16.0.0 255.255.0.0
no shutdown
exit
copy running-config startup-config
```

## Configuración router 2

```bash
enable
configure terminal

interface serial 0/1/0
ip address 162.16.0.2 255.255.0.0
no shutdown
exit

interface fastEthernet 0/0
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

interface serial 0/0
ip address 162.17.0.1 255.255.0.0
no shutdown
exit

copy running-config startup-config
```

## Configuración router 3

```bash
enable
configure terminal

interface serial 0/1/0
ip address 162.17.0.2 255.255.0.0
no shutdown
exit

interface fastEthernet 0/0
ip address 192.168.3.1 255.255.255.0
no shutdown
exit

copy running-config startup-config
```

## Configuración de rutas estáticas

## Router 1

```bash
enable
configure terminal

#ip route <red a alcanzar, mascara de red a alcanzar, mediante que interfaz>

ip route 192.168.2.0 255.255.255.0 162.16.0.2  
ip route 162.17.0.0 255.255.0.0 162.16.0.2
ip route 192.168.3.0 255.255.255.0 162.16.0.2
exit

copy running-config startup-config
```

## Router 2

```bash
enable
configure terminal

ip route 192.168.1.0 255.255.255.0 162.16.0.1
ip route 192.168.3.0 255.255.255.0 162.17.0.2

exit

copy running-config startup-config
```

## Router 3

```bash
enable
configure terminal

ip route 192.168.1.0 255.255.255.0 162.17.0.1
ip route 162.16.0.0 255.255.0.0 162.17.0.1
ip route 192.168.2.0 255.255.255.0 162.17.0.1
exit

copy running-config startup-config
```

