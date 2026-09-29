# Memoria tecnica: nodo Proxmox individual

La memoria presenta el resultado tecnico por temas. Debe explicar como esta
construido el laboratorio, por que se tomaron las decisiones y con que pruebas
se demostro su funcionamiento. Usa la bitacora como fuente, pero no copies el
diario completo: resume la configuracion final, los resultados y las
limitaciones que sigan abiertas.

## Resumen

Se va a construir un laboratorio basado en Proxmox para desplegar y administrar un servicio Linux, 
automatizando su configuración con Bash y Ansible. Está pensado como proyecto formativo del ciclo de ASIR. 
El resultado esperado es un servicio funcional, documentado y con copias de seguridad.

## Necesidad y objetivos

Necesidad que atiende el laboratorio: disponer de un entorno propio y controlado donde practicar la administración de servidores Linux y la virtualización.

- Objetivo comprobable 1: Instalar Proxmox y acceder a su panel web.
- Objetivo comprobable 2: Desplegar una máquina virtual Linux con un servicio de red funcionando.
- Objetivo comprobable 3: Automatizar la configuración del servicio con un script Bash y un playbook de Ansible.

## Alcance y criterios de aceptacion

Instalación de Proxmox, tener un servicio de Linux y su automatización.

## Modalidad e inventario

[Inventario](01-inventario-hardware.md)

## Diseno de red

[Diagrama y direccionamiento](02-diagrama-red.md)

## Instalacion y configuracion de Proxmox

[Procedimiento](03-instalacion-proxmox.md)

## Plantilla Linux y despliegue

[Plantilla](04-plantilla-linux.md) y [VMs/LXC](05-vms-lxc.md)

## Servicio ASO

[Instalacion y pruebas](06-servicio-aso.md)

## Automatizacion con Bash y Ansible

[Automatizacion](07-automatizacion.md)

## Copias y restauracion

[Procedimiento y validacion](08-copias-restauracion.md)

## Rendimiento y seguridad

[Resultados](09-rendimiento-seguridad.md)

## Incidencias y decisiones

[Bitacora](00-bitacora.md)

## Presupuesto y sostenibilidad

## Conclusiones

## Fuentes y anexos

[Evidencias](../evidencias/README.md) y [volver al indice](../README.md)
