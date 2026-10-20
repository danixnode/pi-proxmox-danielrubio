# Diseno de red

## Objetivo

Dejar diseñada la red del laboratorio antes de instalar nada: cómo se conectan el nodo Proxmox, srv-aso y cli-linux, qué IP usa cada uno y por dónde entra y sale el tráfico. Sigo el montaje de la guía de redes del profesor, que indicó usar la red 192.168.56.X para el laboratorio, con el DNS de Google.

## Diseno

- Nombre del nodo Proxmox: pve-rubio.
- Interfaz de gestion: el segundo adaptador de la VM de VirtualBox, en modo solo-anfitrión. Desde ahí entro al panel web de Proxmox con el navegador de mi PC (https://192.168.56.10:8006). El nombre real de la interfaz dentro de Proxmox lo confirmaré al instalarlo.
- Bridge de Proxmox: uso dos. vmbr0 va unido al adaptador 1 de VirtualBox (NAT) y es la salida del nodo a Internet, con la IP 10.0.2.15/24. vmbr1 va unido al adaptador 2 (solo-anfitrión) y es donde están la gestión y las máquinas del laboratorio, con la IP 192.168.56.10/24.
- Red o VLAN de laboratorio: la subred 192.168.56.0/24 (máscara 255.255.255.0). No uso VLAN, porque no son obligatorias en el proyecto.
- IPs de las VMs/LXC: el profesor dijo que usáramos la red 192.168.56.X y que eligiéramos el número de host. Yo uso los de la guía: mi PC anfitrión tiene la 192.168.56.1, el nodo la .10, srv-aso la .20 y cli-linux la .30. Están todas en la misma subred, así que se ven directamente a través del bridge sin pasar por el gateway.
- Gateway y DNS: el gateway de srv-aso y cli-linux es el nodo (192.168.56.10), que tendrá que reenviar el tráfico y hacer NAT hacia vmbr0. El gateway del propio nodo es 10.0.2.2, el del NAT de VirtualBox. El DNS, tanto del nodo como de las dos máquinas, es el de Google (8.8.8.8), como indicó el profesor.
- Puertos necesarios: están en la tabla de puertos de más abajo.
- Medidas de aislamiento: el profesor ha indicado que se saltan en esta sesión, así que las dejo para más adelante.

## Tabla de direccionamiento

| Elemento | Nombre | IP | Funcion | Recursos |
|---|---|---|---|---|
| Nodo Proxmox | pve-rubio | 192.168.56.10 (vmbr1, gestion) y 10.0.2.15 (vmbr0, salida) | Hipervisor | 2 vCPU, 2 GiB de RAM y 20 GiB de disco |
| Servidor ASO | srv-aso | 192.168.56.20 | Servicio principal | 1 vCPU, 1 GiB de RAM y 10 GiB de disco |
| Cliente | cli-linux | 192.168.56.30 | Pruebas | 1 vCPU, 0,5 GiB de RAM y 8 GiB de disco |

Los vCPU de srv-aso y cli-linux salen de los 2 de la VM de Proxmox, no se suman. El reparto de recursos está explicado en [el inventario](01-inventario-hardware.md).

## Puertos necesarios

| Puerto | Servicio | Desde y hacia | Para que lo uso |
|---|---|---|---|
| 8006/TCP | Panel web de Proxmox | De mi PC (192.168.56.1) al nodo (192.168.56.10) | Administrar Proxmox desde el navegador |
| 22/TCP | SSH | De cli-linux y de mi PC a srv-aso y al nodo | Administración remota y pruebas de conexión |
| 445/TCP | Samba | De cli-linux a srv-aso | Acceder a la carpeta compartida, si al final el servicio ASO es Samba |
| 53/UDP y TCP | DNS | De las máquinas y el nodo al DNS 8.8.8.8, saliendo por el NAT | Resolver nombres |

## Evidencias

## Incidencias

Ninguna por ahora, todavía no he montado nada, esto es solo el diseño.

## Conclusion

El diseño separa la salida a Internet (vmbr0, con NAT) de la red del laboratorio (vmbr1, solo-anfitrión) y deja el nodo y las dos máquinas en la misma subred. Lo comprobaré todo cuando monte la red en la Fase 3.

[Volver al indice](../README.md)
