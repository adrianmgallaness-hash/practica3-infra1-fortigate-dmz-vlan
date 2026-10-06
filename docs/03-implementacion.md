# Implementación por etapas
**Guía inicial. No ejecutar valores pendientes ni presentar estas instrucciones como configuración realizada.**

## 1. Preparar el entorno
Datos recibidos: Adrian Magallanes Feliz, 2025-2139, GNS3, FortiOS 7.0.4, Ubuntu Desktop y Ubuntu Server. Falta registrar modelos de switches, versiones Ubuntu y aplicaciones. Completar direccionamiento y nombres de interfaces. Conectar según el diagrama. Confirmar que FGT1 dispone de GUI y de las funciones de inspección necesarias.

## 2. Configurar los switches
En SW1, crear VLAN 10 y 20; asignar los puertos de clientes en modo acceso; fijar el enlace hacia FGT1 en modo troncal y permitir solo las VLAN utilizadas. Mantener la VLAN nativa sin usuarios y consistente en ambos extremos.

En SW2, crear VLAN 30 y colocar en modo acceso tanto los tres puertos de servidores como el enlace hacia port3 de FGT1. No usar un trunk etiquetado para ese enlace en este diseño.

En ambos switches: hostname, credenciales propias, gestión segura, puertos sin uso apagados en una VLAN sin servicio, negociación de trunk deshabilitada cuando aplique, PortFast y BPDU Guard solo en puertos de equipos finales, y port security con límites adecuados al laboratorio. DHCP snooping y otras protecciones se aplicarán si el modelo las soporta, confiando solo en el camino al servidor DHCP. Documentar comandos realmente usados y exportar running-configs. Ajustar sintaxis al modelo; todavía no hay configuración lista para pegar.

## 3. Configurar FortiGate exclusivamente por GUI
Los nombres de menú son orientativos y deben verificarse en la versión instalada. No utilizar consola, SSH, API ni scripts para configurar o demostrar FGT1.

1. **Network → Interfaces:** configurar WAN, crear subinterfaces VLAN 10 y 20 sobre el puerto LAN y asignar sus gateways /25. Configurar port3 como DMZ /28, sin etiquetado.
2. Habilitar DHCP en cada VLAN con sus respectivos rangos, gateway y DNS. Mantener servidores con IP fija.
3. **Network → Static Routes:** configurar la ruta WAN según el ISP o hipervisor; comprobar si DHCP de WAN ya la creó.
4. Limitar la administración HTTPS a la red/equipo administrador previsto. No habilitar gestión desde WAN o DMZ sin necesidad.
5. **Policy & Objects → Addresses / Services:** crear subredes, hosts y servicios precisos. Completar endpoints de actualización antes de autorizar salida.
6. **Security Profiles → Web Filter:** preparar el perfil de bloqueo de Inventario y el de actualizaciones. Seleccionar inspección SSL compatible y comprobar certificados en clientes.
7. **System → Replacement Messages**, si está disponible: ajustar el mensaje de bloqueo de URL y probarlo. No habilitar bypass para el usuario.
8. **Policy & Objects → Firewall Policy:** aplicar la matriz en orden, con registro y NAT solo hacia WAN. Revisar reglas predeterminadas que puedan permitir tráfico amplio.
9. **Log & Report:** verificar origen, destino, acción y política de cada prueba. Mostrar la configuración y los registros por GUI.
10. Exportar una copia de configuración desde la opción de backup de la GUI. Conservar el respaldo privado y preparar una copia saneada para el repositorio.

## 4. Preparar los servidores
Implementar Caja e Inventario como aplicaciones funcionales y conectar al motor de DB elegido. Registrar paquetes, versiones y repositorios usados. Restringir DB a los dos servidores web y SSH a VLAN 20 en los firewalls de host. No abrir todo el tráfico de la DMZ por compartir subred.

Publicar aplicaciones en los puertos documentados, comprobar credenciales y permisos de aplicación, y registrar cada script que se haya utilizado. No inventar scripts ni exportaciones para completar carpetas.

## 5. Validar y documentar
Ejecutar el plan de pruebas, guardar capturas reales y resultados, corregir fallos y repetir únicamente las pruebas afectadas. Grabar el video cuando el comportamiento cumpla los requisitos.

## Primera sesión en GNS3: montaje e interfaces
1. Crear un proyecto llamado **P3-Infra1-AdrianMagallanesFeliz**.
2. Añadir FGT1, SW1, SW2, dos Ubuntu Desktop y tres Ubuntu Server. Usar switches administrables con soporte de las funciones solicitadas, por ejemplo una imagen Cisco L2 disponible legítimamente. El switch Ethernet básico de GNS3 no permite demostrar toda la seguridad ni exportar una running-config de Cisco.
3. Conectar el nodo NAT o acceso ISP a port1; port2 a SW1; port3 a SW2. Conectar ambos Desktop a SW1 y los tres Server a SW2. Anotar los identificadores reales de puertos.
4. Obtener acceso inicial a la GUI de FGT1 según la imagen instalada. Si todavía no existe acceso web, documentar ese impedimento antes de avanzar: no resolverlo mediante CLI sin aclarar la exigencia del docente.
5. En **Network → Interfaces → Create New → Interface**, crear VLAN10 sobre port2, VLAN ID 10, IP **10.21.39.1/255.255.255.128**. Activar DHCP: **10.21.39.10–10.21.39.126**.
6. Crear VLAN20 sobre port2, VLAN ID 20, IP **10.21.39.129/255.255.255.128**. DHCP: **10.21.39.140–10.21.39.254**.
7. Editar port3, alias DMZ, IP **10.21.40.1/255.255.255.240**, sin DHCP. Usar port2 y port3 como interfaces independientes; si pertenecen a un switch interno, revisar sus dependencias antes de separarlas. Mantener acceso GUI durante el cambio.
8. Configurar Caja **10.21.40.2/28**, Inventario **10.21.40.3/28**, DB **10.21.40.4/28**; gateway **10.21.40.1**. El resolver DNS se completará con el servicio local elegido.
9. Configurar puertos y VLAN en switches, poner los clientes en DHCP y verificar T01. No crear reglas generales de salida para “hacer que funcione”.
10. Capturar la topología y las interfaces reales antes de continuar con las aplicaciones y políticas.

Referencia de versión: [Fortinet, SSL & SSH Inspection en FortiOS 7.0.4](https://docs.fortinet.com/document/fortigate/7.0.4/administration-guide/929997/ssl-ssh-inspection).
