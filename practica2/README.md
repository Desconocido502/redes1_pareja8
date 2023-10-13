# Manual Técnico

## 1. Tabla de las direcciones IP utilizadas:

| Dispositivo   | Interfaz | Dirección IP        | Máscara de Subred |
|---------------|----------|----------------------|-------------------|
| Central       | s1/0     | 10.0.0.1             | 255.255.255.252   |
| Central	    | e0/0	   | 108.168.1.2	      | 255.255.255.248   |
| Central	    | e0/1     | 108.168.2.2          |	255.255.255.248   |
| R2	        | e0/0	   | 108.168.1.1	      | 255.255.255.248   |
| R2	        | e0/1	   | 108.168.0.2	      | 255.255.255.0     |
| R3	        | e0/0	   | 108.168.2.1	      | 255.255.255.248   |
| R3	        | e0/1	   | 108.168.0.3	      | 255.255.255.0     |
| R2-R3(Virtual)| -	       | 108.168.0.1	      | 255.255.255.0     |
| VPC11         | eth0	   | 108.168.0.4	      | 255.255.255.0     |
| Villa_Nueva	| s1/0	   | 10.0.0.2	          | 255.255.255.252   |
| Villa_Nueva	| e0/0	   | 108.178.1.1	      | 255.255.255.248   |
| Villa_Nueva	| e0/1	   | 108.178.2.1	      | 255.255.255.248   |
| R5	        | e0/0	   | 108.178.1.2	      | 255.255.255.248   |git staus
| R5	        | e0/1	   | 108.178.0.2	      | 255.255.255.0     |
| R6	        | e0/0	   | 108.178.2.2          | 255.255.255.248   |
| R6	        | e0/1	   | 108.178.0.3	      | 255.255.255.0     |
| R5-R6(Virtual)| -	       | 108.178.0.1          | 255.255.255.0     |
| VPC12         | eth0     | 108.178.0.4          | 255.255.255.0     |

## 2. Tabla de los rangos de IPs disponibles en cada subred:

### 108.168.1.0/29 (Central - e0/0)
- ID de red: 108.168.1.0
- Máscara de subred: 255.255.255.248
- Total de direcciones IP disponibles: 6
- Primera IP disponible: 108.168.1.1
- Última IP disponible: 108.168.1.6
- Dirección de broadcast: 108.168.1.7

### 108.168.2.0/29 (Central - e0/1)
- ID de red: 108.168.2.0
- Máscara de subred: 255.255.255.248
- Total de direcciones IP disponibles: 6
- Primera IP disponible: 108.168.2.1
- Última IP disponible: 108.168.2.6
- Dirección de broadcast: 108.168.2.7

### 108.168.0.0/24 (Central - e0/1, R2 - e0/1, R3 - e0/1, VPC11, R2-R3 Virtual)
- ID de red: 108.168.0.0
- Máscara de subred: 255.255.255.0
- Total de direcciones IP disponibles: 254
- Primera IP disponible: 108.168.0.1
- Última IP disponible: 108.168.0.254
- Dirección de broadcast: 108.168.0.255

### 108.178.1.0/29 (Villa_Nueva - e0/0)
- ID de red: 108.178.1.0
- Máscara de subred: 255.255.255.248
- Total de direcciones IP disponibles: 6
- Primera IP disponible: 108.178.1.1
- Última IP disponible: 108.178.1.6
- Dirección de broadcast: 108.178.1.7

### 108.178.2.0/29 (Villa_Nueva - e0/1)
- ID de red: 108.178.2.0
- Máscara de subred: 255.255.255.248
- Total de direcciones IP disponibles: 6
- Primera IP disponible: 108.178.2.1
- Última IP disponible: 108.178.2.6
- Dirección de broadcast: 108.178.2.7

### 108.178.0.0/24 (Villa_Nueva - e0/1, R5 - e0/1, R6 - e0/1, VPC12, R5-R6 Virtual)
- ID de red: 108.178.0.0
- Máscara de subred: 255.255.255.0
- Total de direcciones IP disponibles: 254
- Primera IP disponible: 108.178.0.1
- Última IP disponible: 108.178.0.254
- Dirección de broadcast: 108.178.0.255

## 3. Configuración de dispositivos:

### Configuración de Central:

```bash
enable 
configure terminal
hostname Central
interface s1/0 
ip address 10.0.0.1 255.255.255.252
no shutdown
interface e0/0
ip address 108.168.1.2 255.255.255.248
no shutdown
interface e0/1
ip address 108.168.2.2 255.255.255.248
no shutdown

ip route 10.0.0.0 255.255.255.252 10.0.0.2
ip route 108.168.1.0 255.255.255.248 108.168.1.1
ip route 108.168.2.0 255.255.255.248 108.168.2.1
ip route 108.168.0.0 255.255.255.0 108.168.1.1
ip route 108.168.0.0 255.255.255.0 108.168.2.1
ip route 108.178.1.0 255.255.255.248 10.0.0.2
ip route 108.178.2.0 255.255.255.248 10.0.0.2
ip route 108.178.0.0 255.255.255.0 10.0.0.2


exit
do write
```

### Configuración de R2:

```bash

enable 
configure terminal
hostname R2
interface e0/0 
ip address 108.168.1.1 255.255.255.248
no shutdown
interface e0/1
ip address 108.168.0.2 255.255.255.0
no shutdown

glbp 1 ip 108.168.0.1
glbp 1 preempt
dlbp 1 priority 100
glbp 1 load-balancing round-robin
exit

ip route 108.168.1.0 255.255.255.248 108.168.1.2
ip route 10.0.0.0 255.255.255.252 108.168.1.2
ip route 108.178.1.0 255.255.255.248 108.168.1.2
ip route 108.178.2.0 255.255.255.248 108.168.1.2
ip route 108.178.0.0 255.255.255.0 108.168.1.2


exit
do write
```

### Configuración de R5:

```bash

enable 
configure terminal
hostname R5
interface e0/0 
ip address 108.178.1.2 255.255.255.248
no shutdown
interface e0/1
ip address 108.178.0.2 255.255.255.0
no shutdown

standby version 2
standby 2 ip 108.178.0.1
standby 2 priority 110
standby 2 preempt
exit

ip route 108.178.1.0 255.255.255.248 108.178.1.1
ip route 10.0.0.0 255.255.255.252 108.178.1.1
ip route 108.168.1.0 255.255.255.248 108.178.1.1
ip route 108.168.2.0 255.255.255.248 108.178.1.1
ip route 108.168.0.0 255.255.255.0 108.178.1.1


exit
do write
```

### Configuración de SW7
```bash
enable
configure terminal
hostname SW7
interface range e0/2-3
channel-group 1 mode active
exit
do write
```

### Configuración de VPC11

```bash
set pcname VPC11
ip 108.168.0.4/24 108.168.0.1
save
```

## 4.Resumen de los comandos usados:

#### Creación de ruta estática:
```bash
ip route 10.0.0.0 255.255.255.252 10.0.0.2
ip route 108.168.1.0 255.255.255.248 108.168.1.1
ip route 108.168.2.0 255.255.255.248 108.168.2.1
ip route 108.168.0.0 255.255.255.0 108.168.1.1
ip route 108.168.0.0 255.255.255.0 108.168.2.1
ip route 108.178.1.0 255.255.255.248 10.0.0.2
ip route 108.178.2.0 255.255.255.248 10.0.0.2
ip route 108.178.0.0 255.255.255.0 10.0.0.2
```

#### Creación de PortChannel con LACP:
```bash
interface range e0/2-3
channel-group 1 mode active
```

#### Creación de PortChannel con PAGP:
```bash
interface range e0/0-1
channel-group 2 mode desirable
```

#### Creación de IP virtual con HSRP:
```bash
standby version 2
standby 2 ip 108.178.0.1
standby 2 priority 110
standby 2 preempt
```

#### Creación de IP virtual con GLBP:
```bash
glbp 1 ip 108.168.0.1
glbp 1 preempt
dlbp 1 priority 100
glbp 1 load-balancing round-robin
```

#### Configuración de VPC:

##### VPC11
```bash
set pcname VPC11
ip 108.168.0.4/24 108.168.0.1
save
```

##### VPC12
```bash
set pcname VPC12
ip 108.178.0.4/24 108.178.0.1
save
```

## 5. Comandos empleados para la verificación del correcto funcionamiento de los protocolos empleados:

#### Para verificar las rutas estáticas configuradas en los routers
```bash
show ip route
```

#### Para verificar el estado de las interfaces y los PortChannels:
```bash
show interfaces
show etherchannel summary
```

#### Para verificar el estado de HSRP/GLBP y las IP virtuales:
```bash
show standby
show glbp
```

## 6. Resumen de comandos general

##### Tabla de enrutamiento
| DISPOSITIVO  | INTERFAZ | IP               | MÁSCARA |
|--------------|----------|------------------|---------|
| Central      | s1/0     | 10.0.0.1         | /30     |
| Central      | e0/0     | 108.168.1.2      | /29     |
| Central      | e0/1     | 108.168.2.2      | /29     |
| R2           | e0/0     | 108.168.1.1      | /29     |
| R2           | e0/1     | 108.168.0.2      | /24     |
| R3           | e0/0     | 108.168.2.1      | /29     |
| R3           | e0/1     | 108.168.0.3      | /24     |
| R2-R3 (VIRT) | VIRTUAL  | 108.168.0.1      | /24     |
| VPC11        | eth0     | 108.168.0.4      | /24     |
| Villa_Nueva  | s1/0     | 10.0.0.2         | /30     |
| Villa_Nueva  | e0/0     | 108.178.1.1      | /29     |
| Villa_Nueva  | e0/1     | 108.178.2.1      | /29     |
| R5           | e0/0     | 108.178.1.2      | /29     |
| R5           | e0/1     | 108.178.0.2      | /24     |
| R6           | e0/0     | 108.178.2.2      | /29     |
| R6           | e0/1     | 108.178.0.3      | /24     |
| R5-R6 (VIRT) | VIRTUAL  | 108.178.0.1      | /24     |
| VPC12        | eth0     | 108.178.0.4      | /24     |

#### CONFIGURACION LACP SW7-SW8

**SW7**
```bash
enable
configure terminal
hostname SW7
interface range e0/2-3
channel-group 1 mode active
exit
do write
```

**SW8**
```bash
enable
configure terminal
hostname SW8
interface range e0/0-1
channel-group 1 mode active      
exit
do write
```

#### CONFIGURACION PAGP SW9-SW10
**SW9**
```bash
enable
configure terminal
hostname SW9
interface range e0/2-3
channel-group 2 mode desirable
exit
do write
```

**SW10**
```bash
enable
configure terminal
hostname SW10
interface range e0/0-1
channel-group 2 mode desirable
exit
do write
```

##### CONFIGURACION DE ROUTERS

###### Central

```bash
enable 
configure terminal
hostname Central
interface s1/0 
ip address 10.0.0.1 255.255.255.252
no shutdown
interface e0/0
ip address 108.168.1.2 255.255.255.248
no shutdown
interface e0/1
ip address 108.168.2.2 255.255.255.248
no shutdown

ip route 10.0.0.0 255.255.255.252 10.0.0.2
ip route 108.168.1.0 255.255.255.248 108.168.1.1
ip route 108.168.2.0 255.255.255.248 108.168.2.1
ip route 108.168.0.0 255.255.255.0 108.168.1.1
ip route 108.168.0.0 255.255.255.0 108.168.2.1
ip route 108.178.1.0 255.255.255.248 10.0.0.2
ip route 108.178.2.0 255.255.255.248 10.0.0.2
ip route 108.178.0.0 255.255.255.0 10.0.0.2


exit
do write
```

**R2**
```bash
enable 
configure terminal
hostname R2
interface e0/0 
ip address 108.168.1.1 255.255.255.248
no shutdown
interface e0/1
ip address 108.168.0.2 255.255.255.0
no shutdown

glbp 1 ip 108.168.0.1
glbp 1 preempt
dlbp 1 priority 100
glbp 1 load-balancing round-robin
exit

ip route 108.168.1.0 255.255.255.248 108.168.1.2
ip route 10.0.0.0 255.255.255.252 108.168.1.2
ip route 108.178.1.0 255.255.255.248 108.168.1.2
ip route 108.178.2.0 255.255.255.248 108.168.1.2
ip route 108.178.0.0 255.255.255.0 108.168.1.2


exit
do write
```

**R3**
```bash
enable 
configure terminal
hostname R3
interface e0/0 
ip address 108.168.2.1 255.255.255.248
no shutdown
interface e0/1
ip address 108.168.0.3 255.255.255.0
no shutdown

glbp 1 ip 108.168.0.1
glbp 1 load-balancing round-robin
exit

ip route 108.168.2.0 255.255.255.248 108.168.2.2
ip route 10.0.0.0 255.255.255.252 108.168.2.2
ip route 108.178.1.0 255.255.255.248 108.168.2.2
ip route 108.178.2.0 255.255.255.248 108.168.2.2
ip route 108.178.0.0 255.255.255.0 108.168.2.2


exit
do write
```

**Villa Nueva**
```bash
enable 
configure terminal
hostname Villa_Nueva
interface s1/0 
ip address 10.0.0.2 255.255.255.252
no shutdown
interface e0/0
ip address 108.178.1.1 255.255.255.248
no shutdown
interface e0/1
ip address 108.178.2.1 255.255.255.248
no shutdown

ip route 10.0.0.0 255.255.255.252 10.0.0.1
ip route 108.178.1.0 255.255.255.248 108.178.1.2
ip route 108.178.2.0 255.255.255.248 108.178.2.2
ip route 108.178.0.0 255.255.255.0 108.178.1.2 
ip route 108.178.0.0 255.255.255.0 108.178.2.2
ip route 108.168.1.0 255.255.255.248 10.0.0.1
ip route 108.168.2.0 255.255.255.248 10.0.0.1
ip route 108.168.0.0 255.255.255.0 10.0.0.1


exit
do write
```

**R5**

```bash
enable 
configure terminal
hostname R5
interface e0/0 
ip address 108.178.1.2 255.255.255.248
no shutdown
interface e0/1
ip address 108.178.0.2 255.255.255.0
no shutdown

standby version 2
standby 2 ip 108.178.0.1
standby 2 priority 110
standby 2 preempt
exit

ip route 108.178.1.0 255.255.255.248 108.178.1.1
ip route 10.0.0.0 255.255.255.252 108.178.1.1
ip route 108.168.1.0 255.255.255.248 108.178.1.1
ip route 108.168.2.0 255.255.255.248 108.178.1.1
ip route 108.168.0.0 255.255.255.0 108.178.1.1


exit
do write
```

**R6**
```bash
enable 
configure terminal
hostname R6
interface e0/0 
ip address 108.178.2.2 255.255.255.248
no shutdown
interface e0/1
ip address 108.178.0.3 255.255.255.0
no shutdown

standby version 2
standby 2 ip 108.178.0.1
exit

ip route 108.178.2.0 255.255.255.248 108.178.2.1
ip route 10.0.0.0 255.255.255.252 108.178.2.1
ip route 108.168.1.0 255.255.255.248 108.178.2.1
ip route 108.168.2.0 255.255.255.248 108.178.2.1
ip route 108.168.0.0 255.255.255.0 108.178.2.1


exit
do write
```

**VPC11**
```bash
set pcname VPC11
ip 108.168.0.4/24 108.168.0.1
save
```

**VPC11**
```bash
set pcname VPC12
ip 108.178.0.4/24 108.178.0.1
savegit status

```
