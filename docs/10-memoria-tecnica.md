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
