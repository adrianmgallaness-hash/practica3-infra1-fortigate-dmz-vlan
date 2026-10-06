# Pruebas de aceptación y video
**Estado de todas las pruebas: pendiente.** Rellenar resultado real, fecha y evidencia después de ejecutarlas.

Antes de probar, verificar que el servicio destino funciona desde un origen autorizado; un servicio apagado no demuestra que el firewall lo bloqueó. Probar conexiones nuevas para evitar confusión por sesiones previas.

| ID | Prueba | Resultado esperado | Evidencia |
|---|---|---|---|
| T01 | PC-V10 y PC-V20 solicitan DHCP | Dirección /25, gateway y DNS correctos, subredes diferentes | IP de ambos clientes y concesiones GUI |
| T02 | Ambos clientes acceden a Caja | Aplicación funcional | Navegador y log |
| T03 | VLAN 20 accede a Inventario | Aplicación funcional | Navegador y log |
| T04 | VLAN 10 accede a Inventario | Aviso explícito de política infringida; contenido inaccesible | Aviso y evento Web Filter |
| T05 | Repetir T04 por HTTP, HTTPS, IP y alias publicados | Sin bypass ni advertencia TLS que oculte el aviso | Capturas de variantes |
| T06 | VLAN 20 usa SSH hacia cada servidor | Sesión autenticada permitida | Cliente SSH y log |
| T07 | VLAN 10 usa SSH hacia cada servidor | Denegado por política | Cliente y log de denegación |
| T08 | Cada servidor inicia conexión a LAN 10 y 20 | Bloqueada; un servicio destino activo permite comprobarlo | Prueba TCP y logs GUI |
| T09 | Actualizar componentes de cada servidor | Actualizaciones desde destinos aprobados | Resultado del gestor y logs de destinos |
| T10 | DMZ intenta sitios ajenos e IP directa | Bloqueado, incluso si comparten alojamiento con destinos permitidos | Pruebas y logs |
| T11 | DMZ intenta DNS externo y protocolos alternativos | Bloqueado | Pruebas y logs |
| T12 | Caja e Inventario acceden a DB; usuario intenta DB | Web→DB permitido; cliente→DB bloqueado | Aplicaciones, prueba de conexión y registros |
| T13 | Un servidor DMZ intenta SSH a otro | Bloqueado por firewall de host | Registro del host destino |
| T14 | Revisar VLAN, trunks y puertos sin uso | Segmentación y seguridad básica correctas | Configuración de ambos switches |

No limitar T08 a ping: ICMP bloqueado no prueba que TCP esté cerrado. Para T09, una conexión HTTPS exitosa no prueba que el sistema pueda actualizarse; ejecutar la operación real.

## Registro por prueba
Copiar este bloque para cada ID:
- Fecha/hora y versión de configuración:
- Equipo origen y dirección:
- Destino y puerto:
- Acción ejecutada:
- Resultado esperado:
- Resultado observado:
- Captura y log:
- Estado: aprobado / fallido / pendiente.
- Corrección y repetición, si aplica:

## Guion sugerido: 9 minutos 30 segundos
| Tiempo | Demostración |
|---|---|
| 00:00–00:30 | Mostrar fecha/hora, rostro, voz y propósito |
| 00:30–01:20 | Topología, VLAN y DMZ; interfaces por GUI |
| 01:20–02:00 | DHCP de ambos clientes y seguridad de switches |
| 02:00–03:10 | Caja permitida e Inventario bloqueado con aviso en VLAN 10 |
| 03:10–04:20 | Inventario y SSH permitidos desde VLAN 20 |
| 04:20–05:20 | SSH bloqueado desde VLAN 10 |
| 05:20–06:30 | Nuevas conexiones DMZ→LAN bloqueadas y logs GUI |
| 06:30–08:15 | Actualizaciones autorizadas y destinos ajenos bloqueados |
| 08:15–09:00 | Caja/Inventario usando DB y reglas relevantes |
| 09:00–09:30 | Repositorio con documentación, imágenes, scripts y configs |

Ensayar para no exceder 10 minutos. Mostrar demostración real; la documentación cubre detalles que no caben en el video. Toda configuración y demostración del FortiGate debe realizarse por GUI.
