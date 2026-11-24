# Bitacora de sesiones

La bitacora es el registro cronologico del trabajo. Anade una entrada al final
de cada sesion y conserva las anteriores. Escribe datos comprobables y enlaza
las evidencias utiles; evita frases generales como "he trabajado en Proxmox".

## Sesion 01 - 22/09/2026

- Objetivo: crear el repositorio en GitHub y empezar la documentación en Markdown.
- Acciones realizadas: Creé el repositorio `pi-proxmox-danielrubio`. En mi VM Linux instalé Git, configuré mi nombre y el correo noreply de GitHub, y creé una clave SSH que añadí a mi cuenta. Generé la estructura de carpetas con el script del profesor y rellené el README, la ficha inicial, esta bitácora y el inicio de la memoria. Por último hice el primer commit y lo subí.
- Decisiones y motivos: Usé SSH en vez de un token porque se configura una vez y luego no pide nada más. Usé el correo noreply para que mi correo real no salga en los commits.
- Pruebas y resultados: Con `ssh -T git@github.com` GitHub me reconoció. Después del `git push` el repositorio se veía en GitHub con el README y los enlaces funcionando.
- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md) y [README](../README.md).
- Incidencias y solucion o estado pendiente: El script no se ejecutaba porque al guardarlo le había quitado una letra al nombre (`rear_...` en vez de `crear_...`). Lo renombré con `mv` y funcionó.
- Siguiente paso: investigar el hardware del equipo para la fase 1.

## Sesion 02 - 13/10/2026

- Objetivo: investigar el hardware y ver si se puede instalar Proxmox (fase 1).
- Acciones realizadas: Usé el PC del departamento que nos dejó el profesor. Arranqué Hiren's BootCD PE desde un USB para ver los modelos con CPU-Z, HWiNFO y CrystalDiskInfo, y desde LliureX saqué la RAM, el disco, si existe `/dev/kvm` y la versión de VirtualBox. Consulté las webs de Proxmox, Intel, Toshiba y Oracle y con todo eso rellené el inventario.
- Decisiones y motivos: Elegí la modalidad virtualizada con VirtualBox porque ese PC no se puede borrar. Puse como versión prevista Proxmox VE 9.2, que es la última de la web.
- Pruebas y resultados: `/dev/kvm` existe, así que la virtualización estaba activada. El dictamen salió apto con condiciones, sobre todo por la poca RAM y el disco mecánico.
- Documentos o evidencias: [Inventario y viabilidad](01-inventario-hardware.md).
- Incidencias y solucion o estado pendiente: Varias capturas tenían números de serie y la MAC, y los tapé antes de subirlas.
- Siguiente paso: diseñar la red del laboratorio.

## Sesion 03 - 20/10/2026

- Objetivo: diseñar la red del laboratorio (fase 2).
- Acciones realizadas: Ese día no pude ir a clase. Lo hice en casa con la teoría de redes del profesor y lo que me contaron mis compañeros. Rellené el diseño de red con el nombre del nodo, los dos bridges, las IPs, el gateway, el DNS y los puertos.
- Decisiones y motivos: Uso dos redes. `vmbr0` va por NAT y sirve para salir a Internet, y `vmbr1` va por solo anfitrión y es la red del laboratorio, así no toco la red del instituto. Usé la red `192.168.56.0/24`, con el nodo en la `.10`, `srv-aso` en la `.20` y `cli-linux` en la `.30`, y el DNS `8.8.8.8`, como dijo el profesor.
- Pruebas y resultados: No hay pruebas, era solo el diseño.
- Documentos o evidencias: [Diseño de red](02-diagrama-red.md).
- Incidencias y solucion o estado pendiente: El diagrama y el aislamiento se dejaron para más adelante, según lo que dijo el profesor.
- Siguiente paso: crear la VM en VirtualBox e instalar Proxmox.

## Sesion 04 - 27/10/2026

- Objetivo: instalar Proxmox en una VM de VirtualBox (fase 3).
- Acciones realizadas: En mi PC de clase (i3-12100 y 16 GB de RAM) creé la VM `pve-rubio` con 4 vCPU, 8 GB de RAM y un disco de 80 GB, con un adaptador NAT y otro solo anfitrión. Activé PAE/NX, Nested VT-x y Nested Paging y puse el controlador en virtio-scsi. Instalé Proxmox VE 9.2 con el nodo `pve-drubio` en la `10.0.2.15` y añadí a mano `vmbr1` con la `192.168.56.10`.
- Decisiones y motivos: Instalé con ext4, que es lo que viene por defecto. A la VM le di solo la mitad de los hilos y de la RAM para que el PC no se quedara sin recursos.
- Pruebas y resultados: Entré al panel desde el navegador del PC en `https://192.168.56.10:8006`. Dentro del nodo, `grep -c vmx` da 8 y existe `/dev/kvm`, o sea, la virtualización anidada llega a Proxmox.
- Documentos o evidencias: [Instalación de Proxmox](03-instalacion-proxmox.md).
- Incidencias y solucion o estado pendiente: `vmbr1` no cogía IP porque escribí mal la IP y puse `bride` en vez de `bridge`. Lo corregí y reinicié la red. Queda pendiente arrancar una máquina dentro de Proxmox para confirmar del todo que KVM funciona.
- Siguiente paso: configurar la base del nodo (fase 4).

## Sesion 05 - 03/11/2026

- Objetivo: subir la documentación de la instalación y hacer los puntos 1 y 2 de la fase 4.
- Acciones realizadas: Subí las capturas y el documento de la fase 3. Miré los discos, el LVM y los almacenamientos del nodo, creé el almacenamiento `copias-pi` para las copias y comprobé el espacio libre del PC. Relacioné `nic0` y `nic1` con los adaptadores de VirtualBox por la MAC, puse el adaptador 2 en modo promiscuo y cambié los repositorios enterprise por el no-subscription.
- Decisiones y motivos: Las copias van al mismo disco porque no hay otro, y he dejado anotado el riesgo. Quité los repositorios enterprise porque son de pago y daban error.
- Pruebas y resultados: `copias-pi` sale activo, el nodo resuelve `deb.debian.org` y `apt update` ya funciona sin errores.
- Documentos o evidencias: [Instalación y configuración base](03-instalacion-proxmox.md).
- Incidencias y solucion o estado pendiente: Una captura se subió con el nombre `.png.png` y no se veía; la subí bien y borré la mala. Hay que vigilar el espacio del PC, porque el VDI puede crecer hasta 80 GB y solo quedan 71 GB libres.
- Siguiente paso: actualizar la ficha, el inventario y el diseño de red con los datos reales y empezar la plantilla Linux (fase 5).

[Volver al indice](../README.md)
