# Diseño de Red con VLANs y Enrutamiento Inter-VLAN

Este proyecto implementa una red de área local segmentada mediante **VLANs** y configurada para permitir comunicación entre ellas usando **Router-on-a-Stick**.  
La topología fue creada en **Cisco Packet Tracer** y es ideal para prácticas de CCNA o diseño básico de redes LAN.

-- -

## Diseño
<img width="721" height="358" alt="image" src="https://github.com/user-attachments/assets/1f9e25b0-1ba5-4554-8b1e-23e4c16895c6" />

-- -

## Topología del Proyecto

La red está compuesta por:

- 1 Router (Router-on-a-Stick)
- 3 Switches Cisco 2960
- 4 PCs distribuidas entre dos VLANs
- VLAN 10 (color verde en la topología)
- VLAN 20 (color amarillo en la topología)

**VLAN 10:** PC4, PC6  
**VLAN 20:** PC5, PC7

## Configuración Implementada

### Direccionamiento IP

| Dispositivo | VLAN | IP            | Gateway      |
| ----------- | ---- | ------------- | ------------ |
| PC4         | 10   | 192.168.10.10 | 192.168.10.1 |
| PC6         | 10   | 192.168.10.11 | 192.168.10.1 |
| PC5         | 20   | 192.168.20.10 | 192.168.20.1 |
| PC7         | 20   | 192.168.20.11 | 192.168.20.1 |

### 1. Configuración de VLANs en cada switch

#### Switch 1 (izquierda)

```bash
enable
conf t
vlan 10

name VLAN10
vlan 20
name VLAN20
exit

interface range fa0/1-24
switchport mode access
exit

interface fa0/1
switchport access vlan 10     ! PC4
interface fa0/2
switchport access vlan 20     ! PC5

interface fa0/24
switchport mode trunk         ! Enlace hacia Switch central
exit
```

#### Switch central

```bash
enable
conf t

vlan 10
name VLAN10
vlan 20
name VLAN20
exit

interface fa0/1
switchport mode trunk         ! Hacia Switch 1
interface fa0/2
switchport mode trunk         ! Hacia Switch 3
interface fa0/3
switchport mode trunk         ! Hacia el Router
exit
```

#### Switch 3 (derecha)

```bash
enable
conf t

vlan 10
name VLAN10
vlan 20
name VLAN20
exit

interface range fa0/1-24
switchport mode access
exit

interface fa0/1
switchport access vlan 10     ! PC6
interface fa0/2
switchport access vlan 20     ! PC7

interface fa0/24
switchport mode trunk         ! Hacia Switch central
exit
```

### 2. Configuración del Router (Router-on-a-Stick)

En el router:

```bash
enable
conf t

interface g0/0
no shut
exit

! Subinterfaces
interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit
```

### 3. Configurar las PCs

VLAN 10

PC4 →

```bash
IP: 192.168.10.10
Mask: 255.255.255.0
Gateway: 192.168.10.1
```

PC6 →

```bash
IP: 192.168.10.11
Mask: 255.255.255.0
Gateway: 192.168.10.1
```

VLAN 20

PC5 →

```bash
IP: 192.168.20.10
Mask: 255.255.255.0
Gateway: 192.168.20.1
```

PC7 →

```bash
IP: 192.168.20.11
Mask: 255.255.255.0
Gateway: 192.168.20.1
```
