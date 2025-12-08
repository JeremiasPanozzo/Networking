# Diseño e Implementación de una Red Multi-LAN con Enrutamiento entre Subredes

Este proyecto implementa una infraestructura de red compuesta por **cuatro subredes independientes**, interconectadas mediante **dos routers Cisco ISR4331**. Los routers se enlazan a través de una red punto a punto **10.0.0.0/30** y cada uno administra dos LAN, asignando los gateways para las redes:

- **192.168.1.0/24**
- **192.168.2.0/24**    
- **192.168.10.0/24**
- **192.168.20.0/24**

Cada subred cuenta con un switch Cisco 2960 que conecta los equipos finales mediante direcciones IP estáticas.

<img width="1010" height="338" alt="Diseño 1" src="https://github.com/user-attachments/assets/a1a23de0-253a-4af1-887b-60929ccb1f03" />


# Configuración de Routers
## Configuración para ambos routers

```bash
enable
configure terminal

! Hostname
hostname S1   ← Cambiar según el switch

! Proteger acceso local por consola
line console 0
 password cisco
 login

! Proteger acceso remoto por Telnet/SSH
line vty 0 15
 password cisco
 login

! Contraseña para privilegios
enable secret admin123
```

## R1 – Router ISR4331 (lado izquierdo)

```cmd
enable
configure terminal

! --- Configuración de interfaces LAN ---
interface GigabitEthernet0/0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown

interface GigabitEthernet0/0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown

! --- Enlace hacia R2 ---
interface GigabitEthernet0/1/0
 ip address 10.0.0.1 255.255.255.252
 no shutdown

! --- Rutas estáticas hacia las redes remotas ---
ip route 192.168.10.0 255.255.255.0 10.0.0.2
ip route 192.168.20.0 255.255.255.0 10.0.0.2

! --- Finalizar ---
end
copy running-config startup-config
```

## R2 – Router ISR4331 (lado derecho)

```cmd
enable
configure terminal

! --- Configuración de interfaces LAN ---
interface GigabitEthernet0/0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface GigabitEthernet0/0/1
 ip address 192.168.20.1 255.255.255.0
 no shutdown

! --- Enlace hacia R1 ---
interface GigabitEthernet0/1/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown

! --- Rutas estáticas hacia las redes remotas ---
ip route 192.168.1.0 255.255.255.0 10.0.0.1
ip route 192.168.2.0 255.255.255.0 10.0.0.1

R1(config)# end
copy running-config startup-config
```

## Configuración para todos los switches.

```cmd
enable
configure terminal

! Hostname
hostname S1   ← Cambiar según el switch

! Proteger acceso local por consola
line console 0
 password cisco
 login

! Proteger acceso remoto por Telnet/SSH
line vty 0 15
 password cisco
 login

! Contraseña para privilegios
enable secret cisco
```
-- -

### Switch S1 – Subred 192.168.1.0/24

```cmd
enable
configure terminal

hostname S1

! Puertos para PCs
interface range FastEthernet0/1 - 24
 switchport mode access
 spanning-tree portfast

! Puerto hacia R1
interface GigabitEthernet0/1
 switchport mode access

! Dirección de administración del switch (SVI)
interface vlan 1
 ip address 192.168.1.10 255.255.255.0
 no shutdown

! Gateway del switch
ip default-gateway 192.168.1.1

end
copy running-config startup-config
```

### Switch S2 – Subred 192.168.2.0/24

```cmd
enable
configure terminal

hostname S2

interface range FastEthernet0/1 - 24
 switchport mode access
 spanning-tree portfast

interface GigabitEthernet0/1
 switchport mode access

interface vlan 1
 ip address 192.168.2.10 255.255.255.0
 no shutdown

ip default-gateway 192.168.2.1

end
copy running-config startup-config
```

### Switch S3 – Subred 192.168.10.0/24

```cmd
enable
configure terminal

hostname S3

interface range FastEthernet0/1 - 24
 switchport mode access
 spanning-tree portfast

interface GigabitEthernet0/1
 switchport mode access

interface vlan 1
 ip address 192.168.10.10 255.255.255.0
 no shutdown

ip default-gateway 192.168.10.1

end
copy running-config startup-config
```

### Switch S4 – Subred 192.168.20.0/24

```cmd
enable
configure terminal

hostname S4

interface range FastEthernet0/1 - 24
 switchport mode access
 spanning-tree portfast

interface GigabitEthernet0/1
 switchport mode access

interface vlan 1
 ip address 192.168.20.10 255.255.255.0
 no shutdown

ip default-gateway 192.168.20.1

end
copy running-config startup-config
```
