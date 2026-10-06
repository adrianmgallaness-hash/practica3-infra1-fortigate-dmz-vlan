# Matriz de políticas
**Diseño previsto. Todas las pruebas siguen pendientes.**

Aplicar mínimo privilegio y registrar conexiones permitidas y bloqueadas. Las autorizaciones específicas deben preceder a los bloqueos generales. Sin NAT entre redes internas; NAT únicamente para las salidas WAN autorizadas.

| Orden lógico | Origen | Destino | Servicio | Acción |
|---|---|---|---|---|
| 1 | DMZ | VLAN 10 y VLAN 20 | Cualquiera | Denegar y registrar |
| 2 | VLAN 20 | Tres IP de servidores | SSH TCP/22 | Permitir y registrar |
| 3 | VLAN 10 | Tres IP de servidores | SSH TCP/22 | Denegar y registrar |
| 4 | VLAN 10 | IP Inventario | HTTP/HTTPS | Política con inspección web que bloquee y muestre aviso |
| 5 | VLAN 10 y VLAN 20 | IP Caja | Puerto web publicado | Permitir |
| 6 | VLAN 20 | IP Inventario | Puerto web publicado | Permitir |
| 7 | VLAN 10 y VLAN 20 | DMZ | Cualquier otro | Denegar |
| 8 | Servidores DMZ | Endpoints de actualización aprobados | Puertos mínimos verificados | Permitir con filtro de destinos y NAT |
| 9 | DMZ | WAN | Cualquier otro | Denegar y registrar |
| Final | Redes sin autorización expresa | Cualquier destino | Cualquiera | Denegación por defecto |

Separar las políticas por interfaz de entrada/salida cuando lo requiera FortiOS. El acceso web propuesto es HTTPS; abrir HTTP solo si se utiliza y se documenta. No permitir acceso directo de clientes a DB. No crear publicación WAN de servidores, VPN ni reglas amplias LAN–DMZ.

Las respuestas a conexiones permitidas utilizan el seguimiento de estado; bloquear nuevas conexiones DMZ→LAN no debe impedir las respuestas del servidor a una sesión legítima LAN→DMZ.

## Bloqueo visible del Inventario
Una regla de denegación de tráfico por sí sola no acredita el aviso en el navegador. La propuesta utiliza una política exclusiva VLAN 10→IP Inventario, HTTP/HTTPS, con Web Filter de bloqueo y mensaje de reemplazo, antes del cierre general LAN→DMZ.

Como esa política solo apunta al Inventario, su filtro puede bloquear todas las URL, incluyendo acceso por IP y nombres alternativos. La acción de la política de transporte permite inspección; el perfil web debe bloquear la petición antes de entregar el contenido. Validar compatibilidad en la versión real y registrar el evento Web Filter.

Texto previsto: **“Acceso bloqueado: ha infringido la política de seguridad. La VLAN 10 no está autorizada para acceder al Sistema de Inventario.”**

Para HTTPS, el certificado usado en el aviso debe ser confiable en el cliente. Comprobarlo en el navegador: una advertencia de certificado o un timeout no reemplazan el aviso requerido. Comprobar HTTP, HTTPS, IP directa y cada nombre publicado; cerrar otros puertos.

## Actualizaciones exclusivamente autorizadas
Antes de abrir salida, inventariar sistema operativo, motor DB, servidor web y método de actualización.

| Servidor | Componente / versión | FQDN exacto | Puerto | URL oficial que lo justifica | Redirecciones/CDN | Resultado |
|---|---|---|---|---|---|---|
| Caja | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente | No probado |
| Inventario | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente | No probado |
| DB | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente | No probado |

No autorizar Internet completo mientras se recopilan destinos. Elegir repositorios y mirrors concretos. Un objeto FQDN filtra las IP resueltas: una IP compartida puede alojar sitios ajenos. Combinarlo con una lista web de dominios autorizados y bloqueo del resto; ajustar inspección TLS según la versión y los clientes. Si hace falta restringir rutas HTTPS, la inspección de certificado por sí sola no ve esas rutas. Verificar actualización real, redirecciones, dominios ajenos e IP directa antes de declarar cumplimiento.

Proporcionar DNS mediante un resolver local controlado o un servicio de FGT1 que se configure y pruebe por GUI. No permitir DNS externo arbitrario desde la DMZ. Si hacen falta NTP o validación de certificados, resolverlos localmente cuando sea posible y documentar las dependencias; no añadir excepciones externas genéricas. Bloquear protocolos alternativos no necesarios, incluido QUIC, para evitar vías sin inspección.

## Referencias oficiales
- [Fortinet: Static URL filter, 7.4.8](https://docs.fortinet.com/document/fortigate/7.4.8/administration-guide/371670): configuración del filtro y aplicación a la política.
- [Fortinet: Certificate inspection](https://docs.fortinet.com/document/fortigate/latest/administration-guide/505842/certificate-inspection): certificados y páginas HTTPS de reemplazo.
- [Fortinet: Wildcard FQDN, 7.6.4](https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/217973): aprendizaje de direcciones mediante DNS.

Son referencias de diseño. Confirmar menús y capacidades con la versión instalada.

Versión del laboratorio confirmada: FortiOS 7.0.4. [Comparación oficial de modos de inspección 7.0.4](https://docs.fortinet.com/document/fortigate/7.0.4/administration-guide/922096). Configurar el mismo modo en perfil y política; verificar la página de reemplazo en el entorno real.
