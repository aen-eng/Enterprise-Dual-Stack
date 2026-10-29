
# Enterprise Dual Stack SuperLab

## 📌 Descripción

Laboratorio de red empresarial desarrollado en **Cisco Packet Tracer**, integrando tecnologías de switching, routing IPv4/IPv6, servicios de red y seguridad.

El objetivo fue configurar una topología integrada, verificar la conectividad entre sus diferentes segmentos y resolver problemas mediante comandos de diagnóstico.

## 🛠️ Tecnologías implementadas

### Switching
- VLANs y trunks 802.1Q.
- VTP versión 2.
- EtherChannel mediante LACP.
- Rapid PVST+.

### Routing
- Router-on-a-Stick.
- OSPFv2, área 0.
- Routing estático IPv6.
- Dual Stack (IPv4/IPv6).

### Servicios de red
- DHCP.
- NAT/PAT.
- SSH.
- NTP.
- Syslog.
- SNMPv2c.

### Seguridad
- ACLs IPv4 e IPv6.
- Port Security: `shutdown`, `restrict` y `protect`.
- DHCP Snooping.
- Dynamic ARP Inspection (DAI).

## 🌐 Topología

La red está compuesta por:

- **3 routers Cisco 2911:** R1, R2 y R3.
- **3 switches Cisco 2960:** SW1, SW2 y SW3.
- **4 PCs:** ADMIN, VENTAS, IT y PRUEBA IT.
- **1 servidor interno:** SRV1.
- **1 servidor externo:** SRV-EXTERNO.

Los switches forman una topología redundante con enlaces trunk y EtherChannel.

R1 y R2 proporcionan conectividad a las VLAN mediante Router-on-a-Stick. R3 conecta la red interna con el segmento externo.

### VLANs y direccionamiento IPv4

| VLAN | Segmento | Red IPv4 |
|---|---|---|
| 10 | ADMIN | 192.168.10.0/24 |
| 20 | VENTAS | 192.168.20.0/24 |
| 30 | IT | 192.168.30.0/24 |
| 99 | Administración | 192.168.99.0/24 |
| 100 | Servidores | 172.16.10.0/24 |

### Direccionamiento IPv6

| Segmento | Red IPv6 |
|---|---|
| ADMIN | 2001:DB8:10::/64 |
| VENTAS | 2001:DB8:20::/64 |
| IT | 2001:DB8:30::/64 |
| Servidores | 2001:DB8:100::/64 |

## 🔐 Acceso a los routers

Los routers R1, R2 y R3 tienen configurada una contraseña para acceder al modo privilegiado.

**Contraseña de `enable`: `Cisco123!`**

También se configuró SSH para administración remota, con acceso controlado mediante ACLs.

> Las credenciales corresponden exclusivamente a este laboratorio de Packet Tracer.

## 🔍 Configuración y verificación

Se realizaron pruebas para comprobar:

- Propagación de VLANs mediante VTP.
- Funcionamiento de trunks y EtherChannel.
- Elección de root bridge mediante Rapid PVST+.
- Adyacencias OSPF.
- Conectividad IPv4 e IPv6.
- Asignación de direcciones mediante DHCP.
- Traducciones NAT/PAT.
- Restricciones de acceso mediante ACLs.
- Acceso remoto mediante SSH.
- Sincronización NTP.
- Recepción de eventos Syslog.
- Modos de violación de Port Security.

## 🛡️ DHCP Snooping y DAI

Ambas tecnologías se configuraron en **SW1, únicamente para la VLAN 10**.

### DHCP Snooping

Se habilitó DHCP Snooping y se configuró `Gi0/1`, conectada a R1, como interfaz confiable.

`Fa0/1`, conectada a PC1, permaneció como no confiable.

Se verificó la siguiente entrada en la tabla de bindings:

| Dirección IP | Dirección MAC | VLAN | Interfaz |
|---|---|---|---|
| 192.168.10.21 | 00:09:7C:E5:94:B4 | 10 | Fa0/1 |

### Troubleshooting: DHCP Option 82

Después de activar DHCP Snooping, PC1 dejó de obtener una dirección IPv4 mediante DHCP.

Se identificó un problema relacionado con la inserción de **DHCP Option 82** en Packet Tracer.

La solución aplicada fue:

```cisco
no ip dhcp snooping information option
```

Después de desactivar la inserción de Option 82, PC1 volvió a recibir su dirección IPv4 y DHCP Snooping permaneció habilitado.

### Dynamic ARP Inspection (DAI)

Se habilitó DAI en VLAN 10 y se configuró `Gi0/1` como interfaz confiable.

Las verificaciones confirmaron:

- DAI habilitado y activo.
- `Gi0/1` configurada como `Trusted`.
- `Fa0/1` configurada como `Untrusted`.
- Conectividad de PC1 con su gateway.
- Resolución ARP dinámica del gateway.

**Limitación:** los contadores de inspección permanecieron en cero, por lo que no fue posible confirmar el procesamiento o bloqueo efectivo de paquetes ARP.

## ⚠️ Limitaciones de Packet Tracer

### SNMP

Se configuró SNMPv2c en R1, R2 y R3.

La configuración se comprobó mediante `show running-config`, pero no fue posible realizar pruebas completas de consultas SNMP desde un administrador de red.

### DAI

DAI quedó habilitado en VLAN 10 y la conectividad legítima funcionó correctamente.

Sin embargo, Packet Tracer no permitió confirmar completamente la inspección efectiva de paquetes ARP mediante los contadores disponibles.

## 📚 Comandos de verificación

```cisco
show vlan brief
show interfaces trunk
show etherchannel summary
show spanning-tree
show vtp status
show ip interface brief
show ip route
show ipv6 route
show ip ospf neighbor
show ip nat translations
show access-lists
show port-security
show port-security interface fa0/1
show ip dhcp snooping
show ip dhcp snooping binding
show ip arp inspection
show ip arp inspection interfaces
show ntp status
show ntp associations
show logging
show running-config
```

También se realizaron pruebas de conectividad mediante `ping` y verificaciones de acceso remoto por SSH.

## 🎯 Conclusión

El laboratorio permitió integrar tecnologías de switching, routing, servicios y seguridad dentro de una misma topología empresarial.

Se reforzó especialmente la importancia de verificar las configuraciones, interpretar los resultados de los comandos `show` y resolver problemas sin desactivar innecesariamente los mecanismos de seguridad.

El problema encontrado con DHCP Option 82 fue uno de los principales ejercicios de troubleshooting del laboratorio.


