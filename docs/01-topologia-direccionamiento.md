# Topología y direccionamiento
**Propuesta pendiente de implementación.**

## Equipos y conexiones
| Equipo | Función | Conexión propuesta |
|---|---|---|
| FGT1 | Firewall y gateway | port1: WAN; port2: troncal LAN; port3: DMZ |
| SW1 | Switch LAN | Troncal hacia FGT1; acceso VLAN 10 y VLAN 20 |
| SW2 | Switch DMZ | Acceso VLAN 30 hacia port3 y los tres servidores |
| PC-V10 | Usuario VLAN 10 | SW1, dirección por DHCP |
| PC-V20 | Usuario VLAN 20 | SW1, dirección por DHCP |
| SRV-CAJA | Web de Caja | SW2, IP fija |
| SRV-INVENTARIO | Web de Inventario | SW2, IP fija |
| SRV-DB | Base de datos | SW2, IP fija |

La VLAN 30 es una elección de diseño. En esta propuesta port3 del FortiGate recibe tráfico sin etiqueta desde un puerto de acceso de SW2. No crear una subinterfaz etiquetada en port3 sin cambiar también el enlace del switch.

No conectar SW1 con SW2. No habilitar enrutamiento entre VLAN en los switches: todas las conexiones LAN–DMZ deben pasar por FGT1.

## Plan de direcciones propuesto para 2025-2139
Autor: Adrian Magallanes Feliz. Plataforma: GNS3. Firewall: FortiOS 7.0.4. Clientes: Ubuntu Desktop; servidores: Ubuntu Server (asignación propuesta; versiones pendientes).

Criterio: separar los últimos cuatro dígitos 2139 en 21 y 39 para formar 10.21.39.0/24. Dividirlo en dos /25 para los usuarios y reservar 10.21.40.0/28 para la DMZ. Es una propuesta propia, no una fórmula atribuida al docente. Comprobar que no se superponga con WAN, hipervisor o redes existentes.

| Segmento | Prefijo | Máscara | Capacidad útil | Asignación |
|---|---|---|---|---|
| VLAN 10 | /25 | 255.255.255.128 | 126 | Gateway fijo; cliente por DHCP |
| VLAN 20 | /25 | 255.255.255.128 | 126 | Gateway fijo; cliente por DHCP |
| DMZ | /28 | 255.255.255.240 | 14 | Gateway y tres servidores fijos |
| WAN | Según entorno | Pendiente | Según entorno | Gateway y ruta por defecto |

Se interpreta /25 como una subred independiente para cada VLAN. Confirmar si el docente prescribe otra distribución.

Dentro de cada subred, reservar la primera dirección útil para FGT1. En la DMZ, reservar la segunda, tercera y cuarta para Caja, Inventario y DB. No asignar la dirección de red ni broadcast. Excluir gateway y reservas de los rangos DHCP.

| Objeto a completar | Valor |
|---|---|
| NET_V10 / GW_V10 / rango DHCP | 10.21.39.0/25 · 10.21.39.1 · 10.21.39.10–10.21.39.126 |
| NET_V20 / GW_V20 / rango DHCP | 10.21.39.128/25 · 10.21.39.129 · 10.21.39.140–10.21.39.254 |
| NET_DMZ / GW_DMZ | 10.21.40.0/28 · 10.21.40.1 |
| IP_CAJA / IP_INVENTARIO / IP_DB | 10.21.40.2 · 10.21.40.3 · 10.21.40.4 |
| WAN / gateway ISP | Pendiente |
| Resolver DNS local o servicio de FGT1 | Pendiente |

## Límites del diseño
Los tres servidores comparten la misma subred: su tráfico mutuo no atraviesa FGT1. Restringir en el firewall de cada servidor el acceso SSH a NET_V20 y la base de datos a las IP de Caja e Inventario, en el puerto del motor elegido. Esto evita que un servidor DMZ use SSH hacia otro.

No habilitar IPv6 sin controles equivalentes a IPv4. Ningún servidor debe tener una segunda interfaz que permita evitar el firewall.

Broadcast: VLAN 10 = 10.21.39.127; VLAN 20 = 10.21.39.255; DMZ = 10.21.40.15. Rango útil DMZ: 10.21.40.1–10.21.40.14. Los servidores usan 10.21.40.1 como gateway.
