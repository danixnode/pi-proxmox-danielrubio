# Inventario investigado y viabilidad

## Contexto de la observacion

- Fecha y sistema utilizado: 13/10/2026. Para ver el hardware arranqué el PC del departamento con Hiren's BootCD PE desde un USB, así que no instalé ni toqué nada del disco. La RAM, el disco, el KVM y la versión de VirtualBox los miré desde LliureX, que es el sistema que trae el equipo.
- Modalidad, equipo anfitrion y VM VirtualBox si procede: Virtualizada, con Proxmox dentro de una VM de VirtualBox. Este inventario es de un PC del departamento que el profesor nos dejó solo para la fase 1. Tenía VirtualBox 7.0.12 instalado. Al final el proyecto lo he montado en mi PC de clase, que está al final de este documento.
- Version de Proxmox prevista: 9.2 (ISO 9.2-1). Es la última que sale en https://www.proxmox.com/en/downloads, que consulté el 13/10/2026.
- Restricciones de acceso: no tuve ninguna. Entré en la BIOS solo para poner el USB como primer dispositivo de arranque y poder iniciar Hiren's, y no cambié nada más.

## Inventario inicial

| Area | Dato observado y evidencia | Especificacion y fuente | Limite o diferencia | Justificacion |
|---|---|---|---|---|
| CPU | Intel Pentium G4560, 2 núcleos / 4 hilos, VT-x ([captura CPU-Z](../evidencias/capturas-o-salidas/s02-cpu.jpg)) | 64 bits, VT-x y EPT (Intel ARK) | Solo 2 núcleos físicos | Puede virtualizar, pero va justo en carga al tener solo dos núcleos físicos |
| RAM | 2 módulos Crucial CT4G4DFS824A.C8FBD2 de 4 GB (8 GB), DDR4-2400 ([captura SPD](../evidencias/capturas-o-salidas/s02-ram.jpg)). Linux ve 7,6 GiB y 5,9 libres ([salida](../evidencias/capturas-o-salidas/s02-datos-equipo.txt)) | DDR4-2133/2400 (Intel ARK). 2 GB para Proxmox más las VMs (web de Proxmox) | Poca memoria para Proxmox y 2 VMs | Es poca memoria y me limita el reparto, pero creo que me llega si la ajusto bien |
| Almacenamiento | HDD Toshiba DT01ACA050 de 500 GB, estado Good, 5332 horas de uso ([captura](../evidencias/capturas-o-salidas/s02-disco.jpg)). 391 GiB libres ([salida](../evidencias/capturas-o-salidas/s02-datos-equipo.txt)) | 7200 rpm, SATA 6 Gbit/s, 64 MiB de caché (ficha Toshiba) | Es un HDD y Proxmox recomienda SSD | Hay espacio de sobra, pero será lento |
| Red | Realtek RTL8168/8111 Gigabit, enlace actual a 1 Gbps ([captura](../evidencias/capturas-o-salidas/s02-red.jpg)) | Proxmox recomienda Gigabit (web de Proxmox) | Velocidad real de transferencia sin medir | Suficiente para el laboratorio |
| Plataforma | ASUS PRIME B250M-A, BIOS 0810 (09/08/2017) ([captura](../evidencias/capturas-o-salidas/s02-placa.jpg)). Arranque UEFI, con partición /boot/efi en LliureX ([salida](../evidencias/capturas-o-salidas/s02-datos-equipo.txt)) | Proxmox pide CPU y placa con VT (web de Proxmox) | BIOS de 2017 | No debería dar problemas para arrancar |

## 1. Puedo instalar Proxmox en este equipo?

Comparo lo que tiene el equipo con los requisitos que da Proxmox en su web.

| Componente o requisito | Dato de mi equipo y evidencia | Requisito consultado y fuente | Cumple, no cumple o pendiente |
|---|---|---|---|
| CPU, arquitectura y nucleos/hilos | G4560, 64 bits, 2 núcleos / 4 hilos | 64 bits (Proxmox) | Cumple |
| Soporte de virtualizacion y configuracion | VT-x y /dev/kvm | Intel VT o AMD-V (Proxmox) | Cumple |
| RAM | 8 GB instalados, 7,6 GiB visibles | Mínimo 1 GB (Proxmox) | Cumple el mínimo |
| Almacenamiento | HDD de 500 GB | Un disco; recomendado SSD (Proxmox) | Cumple el mínimo; no el recomendado |
| Red | Gigabit | Una tarjeta de red (Proxmox) | Cumple |
| Plataforma, firmware y arranque | UEFI (/boot/efi) | CPU y placa con VT (Proxmox) | Cumple |

- Puedo instalar y arrancar Proxmox: Sí. Cumple los mínimos: CPU de 64 bits con VT-x, más de 1 GB de RAM, un disco y una tarjeta de red. Además /dev/kvm existe, así que la virtualización está activada en la BIOS. No lo instalaría directamente en el PC, sino dentro de una VM de VirtualBox.
- Puedo ejecutar el servidor ASO y el cliente del proyecto: Con condiciones. Tiene 7,6 GiB de RAM y LliureX ya gasta 1,2 en reposo, solo 2 núcleos físicos y un HDD que no es el SSD que recomienda Proxmox. Creo que entrarían Proxmox, srv-aso y cli-linux, pero justo, y puede que tuviera que usar LXC en vez de VMs.
- Condiciones o comprobaciones pendientes: la virtualización anidada ya la he comprobado en mi PC de clase (dentro de Proxmox sale vmx y existe /dev/kvm). Me queda medir más adelante lo rápido que va el disco.

## 2. Que limitaciones tendre con este hardware?

### Reparto de recursos

Este reparto era para el PC del departamento. El reparto real, en mi PC de clase, está al final.

| Elemento | CPU/vCPU | RAM | Disco | Justificacion |
|---|---|---|---|---|
| Proxmox | 2 vCPU | 2 GiB | 20 GiB | Proxmox recomienda 2 GB para el sistema (web de Proxmox) |
| srv-aso | 1 vCPU | 1 GiB | 10 GiB | Es un servidor de ficheros sencillo, así que prefiero ir con lo mínimo y subirlo si veo que se queda corto |
| cli-linux | 1 vCPU | 0,5 GiB | 8 GiB | Solo se va a usar para pruebas |
| Plantilla, ISOs y copias | No aplica | No aplica | 28 GiB | 8 GiB para plantillas y 20 GiB de ISOs y copias |
| Margen de operacion | No aplica | 1,5 GiB | 14 GiB | Para otros usos y para que Proxmox no se quede sin memoria |

**Calculos y supuestos**

- RAM del anfitrión: 7,6 GiB totales - 5 GiB para la VM de Proxmox = 2,6 GiB para LliureX y VirtualBox. LliureX gasta 1,2 GiB en reposo, así que quedarían unos 1,4 GiB de margen. El manual de VirtualBox 7.0 avisa de que si al anfitrión le queda poca RAM empieza a tirar de swap y se queda casi parado, por eso no le doy más a la VM.
- RAM dentro de la VM: 2 + 1 + 0,5 + 1,5 de margen = 5 GiB. La práctica recomienda 6-8 GB para la VM de Proxmox, pero con 7,6 GiB en total y LliureX encendido no da para tanto.
- Disco: 391 GiB libres - 80 GiB del VDI = 311 GiB libres en el anfitrión. Dentro de la VM: 20 + 10 + 8 + 28 + 14 = 80 GiB. Cuento los 80 GiB como si el VDI estuviera lleno del todo, para quedarme en el peor caso (según el manual, un disco dinámico solo crece según se usa).
- CPU: el PC tiene 4 hilos y le doy 2 vCPU a la VM de Proxmox. El manual de VirtualBox 7.0 aconseja no asignar a una VM más de la mitad de los hilos del anfitrión, y con 2 de 4 cumplo justo eso. Los vCPU de srv-aso y cli-linux no se suman a esos 2, salen de ellos. Las vCPU no son núcleos exclusivos.
- No cuento la swap (511 MiB) como RAM, y supongo LliureX en reposo (1,2 GiB usados según free -h).
- Cargas a la vez: creo que Proxmox, srv-aso y cli-linux podrían estar encendidos a la vez. Clonar o hacer copias mientras uso el servicio sería lento por culpa del HDD.

### Limitaciones

| Limitacion o dato pendiente | Evidencia o estimacion | Consecuencia para el proyecto | Medida y limite que permanece |
|---|---|---|---|
| RAM justa (medido) | free -h: 7,6 GiB totales y 1,2 GiB que gasta LliureX en reposo | Solo podría dar 5 GiB a la VM de Proxmox, menos de los 6-8 GB orientativos | Usar LXC si hace falta y no encender nada más. No arregla que la RAM total sea poca |
| CPU con pocos nucleos (medido) | 2 núcleos y 4 hilos, sin Turbo Boost (Intel ARK) | Proxmox y los dos invitados se reparten solo 2 vCPU | Evitar tener dos cosas pesadas a la vez. Solo se resuelve cambiando de procesador |
| Disco mecanico (tipo medido, lentitud estimada) | HDD de 7200 rpm (CrystalDiskInfo y ficha Toshiba). No he medido su velocidad | Clonar, hacer copias y arrancar varias máquinas a la vez iría lento | Hacer clones y copias de uno en uno |
| Virtualizacion anidada (comprobada) | /dev/kvm existe y el G4560 tiene EPT (Intel ARK). En mi PC de clase, dentro de Proxmox sale vmx y existe /dev/kvm | Puedo crear VMs dentro de Proxmox | Falta arrancar una máquina de verdad. Si fallara, usaría LXC |
| Copias en el mismo disco (prevision) | Solo hay un disco físico | Si el disco falla, pierdo las VMs y las copias a la vez | En mi PC de clase las copias van a `copias-pi`, que está en el mismo disco. El riesgo se queda |

### Medidas que voy a aplicar

- **Ajustar el reparto de recursos:** en este PC le daría 5 GiB de RAM a la VM de Proxmox y no los 6-8 GB orientativos, para que a LliureX y VirtualBox les queden 2,6 GiB. Así el anfitrión no se queda sin memoria, pero no arregla que la RAM total sea poca.
- **Usar LXC cuando proceda:** los contenedores gastan menos RAM que las VMs completas, así que son mi plan B para srv-aso y cli-linux. Ayudan con la memoria, pero no con la lentitud del HDD.
- **Reducir cargas simultaneas:** no clonar, hacer copias y usar el servicio a la vez. Evita que el disco y las 2 vCPU se saturen, pero a cambio todo tarda más.
- **Ampliar un componente:** lo que más ayudaría sería un SSD, porque el problema que más se notaría es el HDD.

Lo que he **observado** es la RAM, los núcleos y el tipo de disco. Lo que queda por **comprobar más adelante** es lo rápido que va el disco y si aguanto las tres máquinas encendidas a la vez.

Este equipo valía, aunque justo. Al final el profesor nos lo dejó solo para la fase 1 y el proyecto lo he montado en mi PC de clase, que tiene más margen.

## 3. Que componentes son mas criticos en mi servidor y por que?

| Prioridad y componente | Funcion en mi servidor | Efecto si es insuficiente | Efecto si falla | Evidencia y justificacion de la prioridad |
|---|---|---|---|---|
| 1. Almacenamiento (HDD Toshiba de 500 GB) | Guarda Proxmox, las VMs/LXC, la plantilla y las copias | Todo va lento al clonar, copiar y arrancar varias máquinas | Pierdo las máquinas y las copias a la vez, porque solo hay un disco | HDD de 7200 rpm con 5332 horas de uso, estado Good y 0 sectores dañados (CrystalDiskInfo). Hoy está sano, pero Proxmox recomienda SSD (ficha de Toshiba y web de Proxmox) |
| 2. RAM (7,6 GiB) | Memoria para Proxmox, srv-aso y cli-linux | No caben todas encendidas y el sistema tira de swap, que es lenta | El nodo se cae, pero los datos del disco siguen ahí | free -h: solo podría dar 5 GiB a la VM de Proxmox |
| 3. CPU (Pentium G4560) | Ejecuta Proxmox y las dos máquinas | Todo se ralentiza si hay dos cosas pesadas a la vez | Nada funciona hasta cambiarla, pero no se pierden datos | 2 núcleos físicos y sin Turbo Boost (Intel ARK); para un Samba con pocos usuarios llega |

**Lo que limita el rendimiento y lo que mas daño haria si falla**

- Limita el rendimiento: el disco duro, que es lo más lento del equipo, y la RAM, que decide cuántas máquinas puedo tener encendidas a la vez.
- Mayor impacto si falla: el disco, porque es el único y ahí están las máquinas, la plantilla y las copias, así que afecta a la integridad de los datos. Si fallan la RAM o la CPU el servicio se para, pero los datos no se pierden.

**Componente que mejoraria primero:** el almacenamiento, con un SSD. Lo priorizo frente a ampliar la RAM porque la RAM la puedo apañar con LXC y repartiéndola mejor, pero la lentitud de un HDD no la puedo arreglar de ninguna forma. Además, el disco es lo que más daño haría si falla.

## Fuentes tecnicas

| Fuente y URL | Version/modelo y fecha de consulta | Afirmacion respaldada | Aplicacion al nodo |
|---|---|---|---|
| Proxmox VE, requisitos de sistema. https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements | La página no indica versión. Consultada el 13/10/2026 | Mínimos: 64 bits con VT, 1 GB de RAM, un disco y una tarjeta de red. Recomendado: 2 GB para Proxmox más las VMs, SSD y red Gigabit | Pregunta 1 (tabla de requisitos), Pregunta 2 (RAM) y Pregunta 3 (almacenamiento) |
| Proxmox, página de descargas. https://www.proxmox.com/en/downloads | Proxmox VE 9.2 (ISO 9.2-1). Consultada el 13/10/2026 | La 9.2 es la última versión disponible | Versión prevista para el nodo |
| Intel ARK, Pentium G4560. https://www.intel.com/content/www/us/en/products/sku/97143/intel-pentium-processor-g4560-3m-cache-3-50-ghz/specifications.html | Pentium G4560. Consultada el 13/10/2026 | 64 bits, 2 núcleos y 4 hilos, VT-x, EPT, VT-d, DDR4-2133/2400 y sin Turbo Boost | Pregunta 1 (CPU y virtualización) y Pregunta 3 (CPU) |
| Toshiba, DT Series Desktop HDD. https://toshiba.semicon-storage.com/content/dam/toshiba-ss-v3/master/en/storage/product/internal-specialty/HDD-DTENB.pdf | DT01ACA050 (documento HDD-DTENB). Consultada el 13/10/2026 | 7200 rpm, SATA 6 Gbit/s, 64 MiB de caché y CMR | Preguntas 2 y 3 (disco) |
| Oracle, VirtualBox User Guide for Release 7.0. https://docs.oracle.com/en/virtualization/virtualbox/7.0/user/EN-VBOX-7-0-USER.pdf | Release 7.0 (F43655-13, junio 2024); en ese PC estaba la 7.0.12. Consultada el 13/10/2026 | Al crear una VM no conviene asignar más de la mitad de los hilos del anfitrión, hay que dejarle RAM suficiente para que no tire de swap, y un disco dinámico crece según se usa | Pregunta 2 (reparto de CPU, RAM y disco) |

## Dictamen de viabilidad

Apto con condiciones para la propuesta actual.

En este PC se puede instalar y arrancar Proxmox sin problema, porque cumple todos los mínimos y la virtualización está activada. Lo que va justo es el proyecto completo: la RAM es poca, el procesador solo tiene 2 núcleos y el disco es un HDD. Aun así, para un Samba con pocos usuarios y un cliente de pruebas llegaría si reparto bien los recursos y uso LXC cuando haga falta.

Las condiciones eran comprobar la virtualización anidada, que ya está comprobada en mi PC de clase, y medir lo rápido que va el disco, que lo haré en la fase de rendimiento.

## Equipo donde he montado el proyecto

Al final el proyecto lo he montado en mi PC de clase, que va mejor que el del departamento.

| Area | PC de clase |
|---|---|
| CPU | Intel Core i3-12100, 4 núcleos y 8 hilos |
| RAM | 16 GB |
| Disco | 198 GB, con 71 GB libres ([captura](../evidencias/capturas-o-salidas/s05-anfitrion.png)) |
| VirtualBox | 7.2 |
| VM de Proxmox | `pve-rubio`: 4 vCPU, 8 GB de RAM y un VDI de 80 GB que ahora ocupa 4,33 GB ([captura](../evidencias/capturas-o-salidas/s05-vbox-disco.png)) |

Reparto dentro de la VM:

| Elemento | CPU/vCPU | RAM | Disco | Justificacion |
|---|---|---|---|---|
| Nodo Proxmox | 4 vCPU compartidas | Unos 2 GB | 29,9 GB del sistema (`local`) | Es lo que gasta Proxmox sin máquinas |
| srv-aso | 2 vCPU | 2 GB | 10 GB en `local-lvm` | Es el servidor, el que más va a trabajar |
| cli-linux | 1 vCPU | 1 GB | 8 GB en `local-lvm` | Solo es para probar el servicio |
| Plantilla, ISOs, copias y margen | No aplica | Unos 3 GB libres | El resto de `local-lvm` y `local` (con `copias-pi`) | Margen para las siguientes fases |

A la VM le doy la mitad de los hilos y de la RAM del PC, así que a LliureX le quedan 4 hilos y unos 8 GB. Los recursos de srv-aso y cli-linux salen de los de la VM, no se suman. Lo único que hay que vigilar es el disco: el VDI puede llegar a 80 GB y en el PC quedan 71 libres, y además lo comparto con las máquinas de otras asignaturas.

## Evidencias y revision docente

Los datos de hardware del PC del departamento los saqué con CPU-Z, HWiNFO y CrystalDiskInfo desde Hiren's BootCD PE. En las capturas he tapado los números de serie y la MAC.

**CPU (CPU-Z, pestaña CPU).** Un Pentium G4560 (Kaby Lake) con 2 núcleos, 4 hilos y VT-x en la lista de instrucciones, así que puede virtualizar. La frecuencia que sale (unos 800 MHz) es porque estaba en reposo, no es la máxima (3,50 GHz según Intel).

![CPU-Z, pestaña CPU](../evidencias/capturas-o-salidas/s02-cpu.jpg)

**Placa base (CPU-Z, pestaña Mainboard).** Una ASUS PRIME B250M-A con chipset Intel B250 y BIOS 0810 del 09/08/2017. Es de la misma generación que el procesador, con la BIOS antigua.

![CPU-Z, pestaña Mainboard](../evidencias/capturas-o-salidas/s02-placa.jpg)

**RAM (CPU-Z, pestaña SPD).** El slot 1 tiene un módulo Crucial de 4 GB, DDR4-2400. Hay dos iguales, así que son 8 GB en total, aunque la captura solo enseña el slot 1.

![CPU-Z, pestaña SPD](../evidencias/capturas-o-salidas/s02-ram.jpg)

**Disco (CrystalDiskInfo).** Un Toshiba DT01ACA050 de 500 GB, estado Good, 7200 rpm, SATA/600 y 5332 horas encendido, con 0 sectores dañados. Está sano, pero es mecánico.

![CrystalDiskInfo, disco](../evidencias/capturas-o-salidas/s02-disco.jpg)

**Red (HWiNFO).** Una Realtek RTL8168/8111 Gigabit con el enlace a 1 Gbps.

![HWiNFO, tarjeta de red](../evidencias/capturas-o-salidas/s02-red.jpg)

**Salida de comandos en LliureX** ([s02-datos-equipo.txt](../evidencias/capturas-o-salidas/s02-datos-equipo.txt)). Son free -h, df -h, ls -l /dev/kvm y VBoxManage --version, del 13/10/2026. Salen 7,6 GiB de RAM, 391 GiB libres, /dev/kvm existe y VirtualBox 7.0.12. El pendrive de Hiren's (/dev/sdb1) también sale en df, pero no es un disco del equipo.

- Pregunta de defensa y respuesta: pendiente, de momento no me han hecho ninguna.
- Revisión docente: el profesor lo revisó en la nota de seguimiento hasta la fase 4. Me dijo que el diseño y el inventario no cuadraban en los recursos del nodo, y por eso he añadido el apartado del PC de clase con el reparto real.

[Volver al indice](../README.md)
