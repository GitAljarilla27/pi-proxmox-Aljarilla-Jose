# Memoria Técnica - Despliegue de Infraestructura Proxmox VE

## 1. Resumen inicial
Diseño, despliegue y gestión de una infraestructura virtualizada sobre Proxmox VE para alojar servicios Linux, aplicando automatización en la configuración y despliegue.

## 2. Necesidad
Disponer de un entorno aislado, flexible y documentado para la ejecución y mantenimiento de servicios informáticos en red bajo un hipervisor tipo bare-metal/virtualizado.

## 3. Objetivos comprobables
1. Despliegue del nodo Proxmox con acceso administrativo vía interfaz web y SSH.
2. Segmentación de red funcional para aislar tráfico de administración y de servicios.
3. Despliegue y configuración automatizada de máquinas mediante scripts Bash y playbooks de Ansible.

## 4. Alcance inicial
- **Dentro de alcance:** Instalación del nodo, configuración de almacenamiento y red, creación de contenedores/VMs y aprovisionamiento de servicios base.
- **Fuera de alcance:** Clústeres de alta disponibilidad (HA) multi-nodo y almacenamiento distribuido Ceph.

## 5. Índice de documentos técnicos
- [00. Ficha inicial](00-ficha-inicial.md)
- [00. Bitácora de trabajo](00-bitacora.md)


## 6. Inventario de Hardware y Recursos del Host (ad-ochoa)

| Recurso | Detalle / Especificación |
|---|---|
| **Sistema base** | Host físico de aula (`ad-ochoa`) |
| **CPU** | Procesador Intel (7th Gen / Xeon E3-1200 v6) con soporte VT-x habilitado |
| **Memoria RAM** | 7,6 GiB (~8 GB físicos) |
| **Disco principal** | 465,8 GB - TOSHIBA DT01ACA050 (HDD SATA) |
| **Unidad Óptica** | 1024M - ATAPI iHAS124 F |
| **Tarjetas de red** | 1x interfaz física (`eth0`) |

## 7. Diseño de Red y Direccionamiento Inicial

| Interfaz / Bridge | Estado / IP Actual | Propósito en el entorno |
|---|---|---|
| `eth0` | DOWN | Interfaz física principal del equipo host |
| `virbr0` | 192.168.122.1/24 | Puente virtual de KVM/libvirt (creado por el host) |
| `docker0` | 172.17.0.1/16 | Puente de red del demonio Docker local |
| `vmbr0` | **Pendiente** | Futura red de administración de Proxmox |
| `vmbr1` | **Pendiente** | Futura red interna/aislada para servicios |
