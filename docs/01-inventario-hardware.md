# Inventario investigado y viabilidad

## Contexto de la observación
* **Fecha y sistema utilizado:** 20 de octubre de 2026, terminal local sobre host `ad-ochoa` (Debian/Linux).
* **Modalidad, equipo anfitrión y VM si procede:** Equipo físico en el aula. Despliegue de Proxmox previsto sobre este hardware bare-metal (o anidado en KVM).
* **Versión de Proxmox prevista:** Proxmox VE 8.x.

## Inventario inicial

| Área | Dato observado y evidencia | Especificación y fuente | Límite o diferencia | Justificación |
|---|---|---|---|---|
| **CPU** | Intel 7th Gen / Xeon v6 (VT-x activo) (`lscpu`, `lspci`) | Soporte de virtualización Intel VT-x hardware. | Limitado a los hilos físicos del procesador. | Obligatorio para ejecutar KVM/QEMU eficientemente en Proxmox. |
| **RAM** | 7.6 GiB (~8 GB) libres y usables (`free -h`) | 8 GB RAM Física | Memoria muy justa para múltiples VMs pesadas. | Determina el número máximo de VMs encendidas simultáneamente. |
| **Almacenamiento** | 465,8 GB TOSHIBA DT01ACA050 (`lsblk`) | Disco duro mecánico (HDD) SATA | Velocidad de lectura/escritura (IOPS) muy inferior a un SSD. | Suficiente en capacidad, pero será el principal cuello de botella en rendimiento. |
| **Red** | 1x Interfaz Gigabit `eth0` (DOWN) (`ip a`) | 1 puerto RJ45 físico | No hay redundancia de red (sin bonding posible con 1 puerto). | Suficiente para entorno de laboratorio y puentes virtuales (`vmbr0`, `vmbr1`). |
| **Plataforma** | Host físico `ad-ochoa` | Arquitectura x86_64 | Sin acceso a gestión remota (IPMI/iLO). | Entorno estándar compatible con la base Debian de Proxmox. |

## 1. ¿Puedo instalar Proxmox en este equipo?

* **Puedo instalar y arrancar Proxmox:** **SÍ**. Los requisitos mínimos oficiales de Proxmox VE exigen una CPU de 64 bits con soporte de virtualización (VT-x disponible) y al menos 2 GB de RAM. El equipo supera estos mínimos con 8 GB de RAM y una CPU compatible.
* **Puedo ejecutar el servidor ASO y el cliente del proyecto:** **SÍ**. Asignando los recursos con cuidado (ej. servidores sin interfaz gráfica y clientes ligeros), los 8 GB de RAM soportan el hipervisor (2 GB) y al menos dos o tres máquinas virtuales simultáneas.
* **Condiciones o comprobaciones pendientes:** Comprobar si Proxmox se instalará de forma nativa borrando el disco actual, o si se desplegará virtualizado dentro del Linux que ya tiene el equipo usando KVM. Verificar la activación del puerto físico `eth0`.

## 2. ¿Qué limitaciones tendré con este hardware?

El hardware tiene dos limitaciones claras: los **8 GB de RAM**, que obligan a ajustar la memoria de las VMs al milímetro, y el **disco HDD Toshiba**, cuyos bajos IOPS harán que el arranque simultáneo de varias máquinas sea lento.

### Reparto de recursos
| Elemento | CPU/vCPU | RAM | Disco | Justificación |
|---|---|---|---|---|
| **Proxmox** | 2 vCPU | 2 GB | 32 GB | Mínimo recomendado para el S.O. base y los servicios del clúster (corosync/pve-cluster). |
| **srv-aso** | 2 vCPU | 2 GB | 20 GB | Un servidor Linux/Windows Server Core sin GUI funciona holgadamente con 2GB. |
| **cli-linux** | 2 vCPU | 2 GB | 20 GB | Cliente con entorno de escritorio ligero (XFCE) para pruebas. |
| **Plantillas, ISOs y copias** | No aplica | No aplica | 50 GB | Espacio reservado en local/local-lvm para imágenes ISO y backups periódicos. |
| **Margen de operación** | Compartido | ~1.6 GB | ~343 GB | RAM libre para cachés y para evitar el colapso del hipervisor (OOM killer). Sobra disco para instantáneas (snapshots). |

*Nota sobre cálculos:* La CPU se puede sobreaprovisionar (overcommit), por lo que asignar 2 vCPUs a cada máquina no es problema. La RAM **no** debe sobreaprovisionarse: 2GB (PVE) + 2GB (SRV) + 2GB (CLI) = 6 GB, dejando ~1.6 GB libres de seguridad.

### Limitaciones críticas
| Limitación | Evidencia o estimación | Consecuencia | Medida y límite que permanece |
|---|---|---|---|
| **Velocidad de Disco (IOPS)** | Disco mecánico HDD (TOSHIBA) | Tiempos de arranque altos y lentitud en actualizaciones. | **Medida:** Evitar encender todas las VMs a la vez. Escalonar el arranque. |
| **Escasez de RAM** | `free -h` marca 7.6 GiB | Imposible desplegar topologías de red grandes (>3 VMs). | **Medida:** Instalar SO sin interfaz gráfica siempre que sea posible. |

## 3. ¿Qué componentes son más críticos en mi servidor y por qué?

| Prioridad y componente | Función | Efecto si es insuficiente | Efecto si falla | Evidencia y justificación |
|---|---|---|---|---|
| **1. Disco Duro (Almacenamiento)** | Almacenar hipervisor y discos virtuales. | Lentitud extrema (cuello de botella de E/S). | Pérdida total del laboratorio, VMs y configuraciones. | Es un HDD tradicional de 465GB; carece de redundancia (no hay RAID). |
| **2. Memoria RAM** | Mantener VMs encendidas. | Las VMs no arrancan o el host mata procesos (OOM). | Cuelgue del host, VMs se apagan de forma abrupta. | 8 GB es el límite bajo para entornos de virtualización modernos. |
| **3. Tarjeta de Red (`eth0`)** | Conectividad con el exterior. | Tráfico lento hacia el cliente/internet. | Pérdida de gestión web y SSH del Proxmox. | Solo hay 1 puerto; no se puede configurar alta disponibilidad (LACP/Bonding). |

* **Componente que mejoraría primero:** **Unidad de estado sólido (SSD)**. Cambiar el HDD por un SSD multiplicaría exponencialmente las operaciones por segundo (IOPS), reduciendo drásticamente los tiempos de carga y mejorando la fluidez global, algo más notable incluso que ampliar la RAM.

## Fuentes técnicas

| Fuente y URL | Versión/modelo y fecha de consulta | Afirmación respaldada | Aplicación al nodo |
|---|---|---|---|
| **Proxmox System Requirements** (pve.proxmox.com/wiki/System_Requirements) | PVE 8.x / 2026-10-20 | Requiere CPU 64bit VT-x y mínimo 2GB RAM. | Confirma que el nodo `ad-ochoa` (8GB RAM, Intel VT-x) es válido. |
| **Intel ARK Processors** (ark.intel.com) | Intel Core 7th Gen / 2026-10-20 | Soporte de virtualización por hardware (VT-x/EPT). | Ratifica la salida de `lscpu` garantizando compatibilidad con KVM. |
| **Debian Wiki: KVM** (wiki.debian.org/KVM) | Debian 12 / 2026-10-20 | Requisitos de red puente (bridge-utils). | Justifica la necesidad de crear `vmbr0` usando la interfaz física `eth0`. |

## Dictamen de viabilidad

* **Apto con condiciones para la propuesta actual.** 
* **Síntesis:** El hardware disponible (`ad-ochoa`, 8GB RAM, Intel VT-x) cumple estrictamente los requisitos para alojar Proxmox VE y sostener un esquema básico de servidor y cliente. Las condiciones críticas para su éxito son la gestión milimétrica de la RAM (no superando las 2-3 máquinas virtuales simultáneas y evitando interfaces gráficas pesadas) y el escalonamiento en el uso de los discos virtuales, dado el evidente cuello de botella que representa el HDD mecánico Toshiba. 


