# Proyecto Intermodular: nodo Proxmox individual

Alumno/a: Daniel Rubio
URL GitHub: https://github.com/danixnode/pi-proxmox-danielrubio

## Objetivo

Montar un laboratorio con Proxmox para desplegar y administrar un servicio Linux, automatizando tareas con Bash y Ansible.

## Modalidad

Virtualizada: Proxmox VE dentro de una máquina virtual de VirtualBox, en mi PC de clase.

## Indice de documentacion

- [Ficha inicial](docs/00-ficha-inicial.md)
- [Bitacora de sesiones](docs/00-bitacora.md)
- [Inventario](docs/01-inventario-hardware.md)
- [Diagrama de red](docs/02-diagrama-red.md)
- [Instalacion de Proxmox](docs/03-instalacion-proxmox.md)
- [Plantilla Linux](docs/04-plantilla-linux.md)
- [VMs y LXC](docs/05-vms-lxc.md)
- [Servicio ASO](docs/06-servicio-aso.md)
- [Automatizacion Bash y Ansible](docs/07-automatizacion.md)
- [Copias y restauracion](docs/08-copias-restauracion.md)
- [Rendimiento y seguridad](docs/09-rendimiento-seguridad.md)
- [Memoria tecnica](docs/10-memoria-tecnica.md)
- [Evidencias](evidencias/README.md)

## Estado

- [x] GitHub creado y primer commit publicado
- [x] URL entregada y acceso docente comprobado
- [x] Modalidad indicada
- [x] Inventario
- [X] Diagrama de red
- [x] Instalacion Proxmox
- [x] Configuracion base del nodo (puntos 1 y 2)
- [ ] Plantilla Linux
- [ ] VMs/LXC
- [ ] Servicio ASO
- [ ] Scripts Bash
- [ ] Ansible
- [ ] Copias y restauracion
- [ ] Rendimiento y seguridad
- [ ] Memoria final

## Estado del entorno

| Elemento | Estado |
|---|---|
| Repositorio GitHub | Creado y entregado |
| Documentación | Fases 0 a 4 subidas |
| Nodo Proxmox | Instalado y configurado (pve-drubio, 192.168.56.10) |
| Red del laboratorio | 192.168.56.0/24, con salida a Internet por NAT |

## Comando de comprobación

    git status
