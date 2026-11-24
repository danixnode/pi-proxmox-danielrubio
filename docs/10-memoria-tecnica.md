# Memoria tecnica: nodo Proxmox individual

La memoria presenta el resultado tecnico por temas. Debe explicar como esta
construido el laboratorio, por que se tomaron las decisiones y con que pruebas
se demostro su funcionamiento. Usa la bitacora como fuente, pero no copies el
diario completo: resume la configuracion final, los resultados y las
limitaciones que sigan abiertas.

## Resumen

Monto un laboratorio con Proxmox VE dentro de una máquina virtual de VirtualBox, en mi PC de clase. Dentro de Proxmox crearé un servidor Linux con un servicio de red y un cliente para probarlo, y automatizaré parte de la configuración con Bash y Ansible. Es un proyecto del ciclo de ASIR y el resultado esperado es un servicio que funcione, documentado y con copias de seguridad.

## Necesidad y objetivos

Necesidad que atiende el laboratorio: tener un entorno propio y controlado donde practicar la administración de servidores Linux y la virtualización, sin tocar la red ni los equipos del instituto.

- Objetivo comprobable 1: Instalar Proxmox y acceder a su panel web. Cumplido: entro al panel desde mi PC en https://192.168.56.10:8006.
- Objetivo comprobable 2: Desplegar una máquina virtual Linux con un servicio de red funcionando y probarlo desde un cliente. Pendiente.
- Objetivo comprobable 3: Automatizar la configuración del servicio con un script Bash y un playbook de Ansible. Pendiente.

## Alcance y criterios de aceptacion

Entra en el proyecto el nodo Proxmox, dos máquinas (srv-aso y cli-linux), un servicio de ASO, scripts Bash, Ansible, copias de seguridad y pruebas de rendimiento y seguridad. Queda fuera cualquier cambio en la red del instituto y el Proxmox del centro. Cada parte la doy por buena cuando la puedo comprobar: que el panel abra, que las máquinas se vean entre ellas, que el servicio responda desde el cliente y que una copia se pueda restaurar.

## Modalidad e inventario

Modalidad virtualizada. El inventario de la fase 1 lo hice en un PC del departamento (Pentium G4560, 8 GB de RAM y disco duro de 500 GB) y salió apto con condiciones, sobre todo por la poca RAM y por ser un disco mecánico. Al final el proyecto lo he montado en mi PC de clase, que va mejor: i3-12100 con 8 hilos, 16 GB de RAM y un disco de 198 GB con 71 GB libres. A la VM de Proxmox le doy la mitad de los hilos y de la RAM para que el PC no se quede corto.

[Inventario](01-inventario-hardware.md)

## Diseno de red

Uso dos redes. `vmbr0` sale a Internet por el NAT de VirtualBox (10.0.2.15, gateway 10.0.2.2) y `vmbr1` es la red del laboratorio en solo anfitrión (192.168.56.0/24), con el nodo en la .10, srv-aso en la .20 y cli-linux en la .30. Así el laboratorio no toca la red del instituto. El DNS es 8.8.8.8. Después de instalar comprobé con `ip -br addr` e `ip route` que la red real es la del diseño.

[Diagrama y direccionamiento](02-diagrama-red.md)

## Instalacion y configuracion de Proxmox

Tengo Proxmox VE 9.2.2 en la VM `pve-rubio` (4 vCPU, 8 GB de RAM y disco de 80 GB), con el nodo `pve-drubio`. Activé la virtualización anidada en VirtualBox y dentro del nodo existe `/dev/kvm`, así que podré crear máquinas KVM. El disco está con LVM: `local` guarda ISO y plantillas, `local-lvm` guarda los discos de las máquinas y `copias-pi` es para las copias. Cambié los repositorios de pago por el no-subscription y ahora `apt update` funciona sin errores. Lo que queda abierto es probar una máquina KVM de verdad y vigilar el espacio del PC, porque el disco de la VM puede crecer hasta 80 GB y solo quedan 71 libres.

[Procedimiento](03-instalacion-proxmox.md)

## Plantilla Linux y despliegue

Pendiente.

[Plantilla](04-plantilla-linux.md) y [VMs/LXC](05-vms-lxc.md)

## Servicio ASO

Pendiente. El servicio previsto es Samba.

[Instalacion y pruebas](06-servicio-aso.md)

## Automatizacion con Bash y Ansible

Pendiente.

[Automatizacion](07-automatizacion.md)

## Copias y restauracion

Pendiente. El destino de las copias (`copias-pi`) ya está preparado, pero está en el mismo disco que todo lo demás, así que si falla ese disco se perderían el laboratorio y las copias.

[Procedimiento y validacion](08-copias-restauracion.md)

## Rendimiento y seguridad

Pendiente.

[Resultados](09-rendimiento-seguridad.md)

## Incidencias y decisiones

- Elegí la modalidad virtualizada porque no tengo un equipo que pueda borrar.
- Separé la salida a Internet (NAT) de la red del laboratorio (solo anfitrión) para no tocar la red del instituto.
- Instalé con ext4 y LVM, que es lo que viene por defecto.
- Quité los repositorios enterprise porque son de pago y daban error.
- El nodo se llama `pve-drubio` y la VM `pve-rubio`, porque al instalar puse otro nombre del que tenía en el diseño.

[Bitacora](00-bitacora.md)

## Presupuesto y sostenibilidad

Pendiente.

## Conclusiones

Pendiente, se escribirán al cerrar el proyecto.

## Fuentes y anexos

[Evidencias](../evidencias/README.md) y [volver al indice](../README.md)
