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

## 7. Diseño de Red y Direccionamiento

### 7.1. Interfaces del anfitrión físico

Durante el inventario inicial se identificaron las siguientes interfaces:

| Interfaz | Dirección / Estado | Función |
|---|---|---|
| `eth0` | DOWN | Interfaz física del anfitrión |
| `virbr0` | 192.168.122.1/24 | Red virtual de KVM/libvirt |
| `docker0` | 172.17.0.1/16 | Red virtual de Docker |

Estos datos corresponden al inventario inicial del equipo anfitrión.

### 7.2. Interfaces del nodo Proxmox

Tras instalar Proxmox VE en VirtualBox, se configuraron dos adaptadores conectados a los siguientes puentes:

| Interfaz | Dirección IP | Función |
|---|---|---|
| `nic0` → `vmbr0` | 10.0.2.15/24 | Salida a Internet mediante NAT de VirtualBox |
| `nic1` → `vmbr1` | 192.168.56.10/24 | Red local y administración del nodo |

La puerta de enlace predeterminada es `10.0.2.2`, asociada a `vmbr0`.

La interfaz web de administración está disponible en:

`https://192.168.56.10:8006`

### 7.3. Red prevista para las máquinas invitadas

| Máquina | Dirección IP prevista | Gateway |
|---|---|---|
| `srv-aso` | 192.168.56.20/24 | 192.168.56.10 |
| `cli-linux` | 192.168.56.30/24 | 192.168.56.10 |

Se ha creado y arrancado `srv-aso`, pero todavía está pendiente de completar y validar su configuración IPv4.

El nodo Proxmox dispone de una regla NAT temporal para la red `192.168.56.0/24`. Su funcionamiento desde los invitados y su persistencia tras reiniciar siguen pendientes de comprobación.
