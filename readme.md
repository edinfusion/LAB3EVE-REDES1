# Configuracion de redundancia STP
todos los equipos deben converger a un solo dispositivo, en este caso el SW_MASTER (Root Bridge)

# Con protocolo Spanning tree enabled protocol ieee

## Primer paso agregar redundancia entre Switchs
- ### **agregar modo trunk cada puerto de redundancia** Ejemplo: #conf term -> #interface ethernet 0/1 -> #switchport trunk encapsulation dot1q -> #switchport mode trunk -> #duplex auto
- ### **validar que protocolo tiene cada switch**
  Para ello se utiliza el comando: sh spanning-tree (si es ieee es el simple spanning tree protocol)
  ### Las instancias de stp son por vlan
  ![stpporvlan](/images/stp1.PNG) en este caso la vlan 1
  ### status de las interfaces para esa vlan (cual es root)
  ![statusroot](/images/stp2.PNG) en este caso el root es et0/0, el concepto de root, es que es el puerto que me va a comunicar al siguiente equipo o switch.

## Paso dos, validar que el root bridge sea el correcto.
- Entonces se valida en SW_L3 quien esta como root (sh spanning-tree) y a ese puerto debe estar conectado el root-bridge
- Se valida que en SW_Master el puerto al que esta conectado SW_L3 sea el root bridge
![statusroot](/images/stp3.PNG)

## Paso4, Balancear (Root Primario y Root secundario)
|             | **MASTER** | **SLAVE** |
|-------------|------------|-----------|
| **VLAN 10** | P          | S         |
| **VLAN 20** | S          | P         |
| **VLAN 30** | P          | S         |
| **VLAN 40** | S          | P         |
| **VLAN 50** | P          | S         |

## Paso 5, Agregar vlans P y S a Master y Slave
- master
  - configure terminal 
  - spanning-tree vlan 10,30,50 root primary
  - spanning-tree vlan 20,40 root secondary
- slave
  - spanning-tree vlan 20,40 root primary
  - spanning-tree vlan 10,30,50 root secondary

## DIAGRAMA SIN REDUNDANCIA EN CAPA 3
 ![diagarmasinredundancia](/images/tempsnip.png)

## DIAGRAMA CON REDUNDANCIA EN CAPA 3
 ![diagramaconredundancia](/images/tempsnip2.png)

## **Con el protocolo ieee, se interrunpio la conexion en el paquete 233 y se reestablecio en el paquete 247 (se perdieron 14 paquetes)**
 ![ieee](/images/stp4.PNG)

# Con protocolo Per-Vlan rapid spanning tree (rstp)
Como ya estan configurados todos los equipos, únicamente se debe cambiar a el protocolo rstp en cada switch. Se debe ingresar los siguientes comandos para realizar el cambio:
- conf ter
- spanning-tree mode rapid-pvst
- ## **Con el protocolo rstp, se interrunpio la conexion en el paquete 6 y se reestablecio en el paquete 21 (se perdieron 15 paquetes)**
 ![rstp](/images/stp5.PNG)

# Multiple spanning tree model mstp
- conf ter
- spanning-tree mode mst
- ## **Con el protocolo mstp, se interrunpio la conexion en el paquete 6 y se reestablecio en el paquete 21 (se perdieron 15 paquetes)**
 ![mstp](/images/stp6.PNG)

## Tabla Comparativa en protocolos stp
|   **STP**   | **RSTP**   | **MSTP**  |
|-------------|------------|-----------|
|       14    |      15    |      15   |



# Comandos
- switchport trunk encapsulation dot1q (este comando encapsula en dot1q para acceso trunkal)
- switchport mode trunk (deja puerto en modo trunk)
- duplex auto (configura el tipo de comunicacion)
- sh running-config (ver configuracion que tiene un switch)
- sh spanning-tree (que protocolo stp tiene determinado dispositivo, ver el puerto root (enlace primario))
- spanning-tree vlan 1 root primary (este comando convierte en root al dispositivo en la vlan especificada)
- sh interfaces status (este comando se ve si el puerto esta en modo trunk, access, si esta activo o no)
- spanning-tree vlan 10,30,50 root primary
- spanning-tree vlan 20,40 root secondary
- Replicar VLANS en switch cliente
  - vtp domain REDES1
  - vtp password REDES1
  - vtp version 2
  - vtp mode client
- Ver camino de VLAN en switch
  - sh spanning-tree vlan 10 
- Ver equipos conectados (vecinos)
  - sh cdp neighbors
- **Guardar configuraciones en switch**
  - **copy  running-config startup-config**