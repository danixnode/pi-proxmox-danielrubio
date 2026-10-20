# Bitacora de sesiones

La bitacora es el registro cronologico del trabajo. Anade una entrada al final
de cada sesion y conserva las anteriores. Escribe datos comprobables y enlaza
las evidencias utiles; evita frases generales como "he trabajado en Proxmox".

## Sesion 01 - 23/09/2026

- Objetivo: Iniciar GitHub y la documentacion Markdown.
- Acciones realizadas: He creado el reposotorio en GitHub. He instalado Git (2.43.0) en el equipo Linux y configuré mi nombre y el correo noreply de GitHub. He generado una clave SSH y la añadí a mí cuenta. Y finalmente he ejecutado el script del profesor para crear la estrucutura del proyecto y he rellanado README y ficha inicial.
- Decisiones y motivos: Para ir subiendo el proyecto a GitHub, he escogido SSH porque se configura una vez y luego no pide creendenciales. Además decidí usar el correo noreply para que mi email real no quede en el hisotrial de commits.
- Pruebas y resultados: He comprobado la conexión con "ssh -T git@github.com" y me salió el mensaje de autenticación correcto saludandome con el nombre de mi usuario.
- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md).
- Incidencias y solucion o estado pendiente: No he tenido ningun tipo de incidencia.
- Siguiente paso: Rellenar la memoria técnica, hacer el primer commit, subirlo a GitHub, dar acceso al profesor y entregar URL en Aules

## Sesion 02 - 13/10/2026

- Objetivo: investigar el hardware del PC de clase y responder a las tres preguntas de la Fase 1 (si puedo instalar Proxmox, qué limitaciones tendré y qué componentes son más críticos).
- Acciones realizadas: Cambié el orden de arranque en la BIOS para iniciar con USB y arranqué Hiren's BootCD PE para sacar los modelos con CPU-Z, HWiNFO y CrystalDiskInfo. Ya en LliureX ejecuté free -h, df -h, ls -l /dev/kvm y VBoxManage --version. Consulté los requisitos y la página de descargas de Proxmox, la ficha de Intel del G4560, la ficha de Toshiba del disco y el manual de VirtualBox 7.0. Con todo eso rellené docs/01-inventario-hardware.md y subí las capturas a evidencias, tapando antes los números de serie y la MAC.
- Decisiones y motivos: Voy en modalidad virtualizada con VirtualBox porque es un PC del departamento que no puedo borrar. Elijo Proxmox 9.2 por ser la última versión de la web. Le daré 5 GiB de RAM y 2 vCPU a la VM de Proxmox, porque el PC tiene 7,6 GiB y 4 hilos y LliureX ya gasta 1,2 GiB. Dejo LXC como plan B si falta memoria y pongo el SSD como mejora prioritaria. El dictamen es apto con condiciones.
- Pruebas y resultados: Esperaba que la virtualización estuviera activada y /dev/kvm existe, así que sí lo está. free -h marca 7,6 GiB de RAM, df -h marca 391 GiB libres, VirtualBox 7.0.12 está instalado y CrystalDiskInfo da el disco como Good con 0 sectores dañados.
- Documentos o evidencias: [Inventario y viabilidad](01-inventario-hardware.md) y [salida de los comandos](../evidencias/capturas-o-salidas/s02-datos-equipo.txt).
- Incidencias y solucion o estado pendiente: Al guardar la salida de los comandos salió un aviso de operación no permitida, que creo que fue de df, pero el fichero se guardó completo. Las capturas del disco, la RAM y la red traían número de serie o MAC y las tapé antes de subirlas. Sigue pendiente probar la virtualización anidada en VirtualBox (necesito la VM de Proxmox) y medir la velocidad real del HDD.
- Siguiente paso: Leer el apartado Processor Tab del manual de VirtualBox 7.0 y empezar docs/02-diagrama-red.md con el nombre del nodo, la subred del laboratorio y las IPs de srv-aso y cli-linux.

## Sesion 03 - 20/10/2026

- Objetivo: diseñar la red del laboratorio (Fase 2) hasta la tabla de direccionamiento, sin diagrama ni aislamiento.
- Acciones realizadas: No pude asistir a clase, así que trabajé en casa con la teoría de redes virtuales del profesor y con lo que me pasaron mis compañeros (usar la red 192.168.56.X, elegir el número de host, DNS 8.8.8.8 y saltar el diagrama y el aislamiento). Rellené docs/02-diagrama-red.md con el nombre del nodo, la interfaz de gestión, los bridges vmbr0 y vmbr1, la subred 192.168.56.0/24, las IPs, el gateway y el DNS, los puertos y la tabla de direccionamiento. También actualicé la ficha inicial con el nombre del nodo y la red.
- Decisiones y motivos: Uso dos bridges: vmbr0 con NAT para la salida a Internet y vmbr1 en solo-anfitrión para la gestión y las máquinas, porque así separo la salida de la red del laboratorio y no toco la red del centro. No uso VLAN porque no son obligatorias. Elegí los hosts .10, .20 y .30 como en la guía del profesor, y el DNS es 8.8.8.8.
- Pruebas y resultados: no hay pruebas, todavía es solo diseño. Lo comprobaré al montar la red en la Fase 3.
- Documentos o evidencias: [Diseño de red](02-diagrama-red.md).
- Incidencias y solucion o estado pendiente: Ninguna. No hago el diagrama ni las medidas de aislamiento porque el profesor ha indicado que se saltan en esta parte.
- Siguiente paso: Empezar la Fase 3 creando la VM de Proxmox en VirtualBox con los dos adaptadores (NAT y solo-anfitrión).

Anadir una entrada con esta estructura en cada sesion. El historial de
GitHub registra los commits; no hace falta incluir el hash del propio
commit dentro de su entrada de bitacora.

[Volver al indice](../README.md)
