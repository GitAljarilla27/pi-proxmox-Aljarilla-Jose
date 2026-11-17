# Bitácora de Sesiones

## Sesión 01 - 2026-10-13

- **Objetivo:** Crear el repositorio remoto en GitHub, estructurar el proyecto en local y documentar la sesión inicial.
- **Acciones realizadas:**
  - Ejecución de `crear_estructura_entrega.sh` en Debian para generar el árbol de trabajo.
  - Creación del repositorio en GitHub en modalidad pública y entrega de enlace.
  - Redacción inicial de README, ficha técnica, bitácora y exclusiones en .gitignore.
- **Pruebas y resultados:**
  - Ejecución de `ls -la` y comprobación de estructura de directorios correcta.
  - Comprobación de estado limpio en local.
- **Decisiones y motivos:**
  - Uso de máquina virtual Debian como nodo de control por compatibilidad con scripts Bash y soporte nativo para Ansible.
- **Incidencias:**
  - Ninguna incidencia relevante durante la inicialización.
- **Siguiente paso:**
  - Desplegar el entorno Proxmox, comprobar conectividad de red y registrar el inventario inicial de hardware/recursos.

## Sesión 02 - 2026-10-20

- **Objetivo:** Registrar el inventario real de hardware del nodo base (`ad-ochoa`) y establecer el esquema de red inicial.
- **Acciones realizadas:**
  - Ejecución de comandos de diagnóstico CLI (`lscpu`, `free -h`, `lsblk`, `ip -br addr`, `lspci`) en el host físico.
  - Actualización de `docs/10-memoria-tecnica.md` insertando las tablas con los datos reales extraídos.
- **Pruebas y resultados:**
  - Confirmación de soporte de virtualización por hardware (VT-x) habilitado.
  - Identificación de puentes de red locales ya existentes (`virbr0`, `docker0`) y de la interfaz principal `eth0` (actualmente en estado DOWN).
- **Decisiones y motivos:**
  - Se documenta la estructura base del host KVM/Docker del aula antes de proceder a aislar o instalar la configuración específica de Proxmox (`vmbr0`, `vmbr1`).
- **Incidencias:**
  - Ninguna incidencia relevante en la recolección de datos.
- **Siguiente paso:**
  - Desplegar Proxmox VE (o su máquina virtual anidada), configurar las interfaces `vmbr0` y `vmbr1`, y comprobar conectividad.
## Sesión 03 - 2026-11-10

- **Objetivo:** Instalar y configurar Proxmox VE en VirtualBox, comprobar la conectividad y verificar los recursos disponibles.
- **Acciones realizadas:**
  - Creación de una máquina virtual con 8 GB de RAM, 2 vCPU y 80 GB de almacenamiento.
  - Instalación de Proxmox VE 9.2.2 con hostname `pve-aljarilla.lab.test`.
  - Configuración de la red solo-anfitrión `vboxnet0` en VirtualBox.
  - Configuración de los puentes `vmbr0` (NAT) y `vmbr1` (solo-anfitrión).
  - Acceso a la interfaz web de Proxmox mediante `https://192.168.56.10:8006`.
  - Comprobación de la virtualización anidada, interfaces de red y almacenamiento.
  - Organización de las capturas de instalación en el repositorio GitHub.
- **Pruebas y resultados:**
  - Se verificó el funcionamiento de `vmbr0` (`10.0.2.15/24`) y `vmbr1` (`192.168.56.10/24`).
  - Se comprobó la puerta de enlace `10.0.2.2`.
  - El comando `grep -Ec '(vmx|svm)' /proc/cpuinfo` devolvió `8`.
  - Se verificó la disponibilidad de los almacenamientos `local` y `local-lvm`.
  - Se confirmó el acceso al panel de administración web.
- **Decisiones y motivos:**
  - Se utilizó VirtualBox para ejecutar Proxmox mediante virtualización anidada.
  - Se configuraron dos adaptadores para separar la conexión NAT de la red de gestión.
  - Se empleó almacenamiento dinámico para evitar reservar inicialmente los 80 GB completos en el anfitrión.
- **Incidencias:**
  - Se corrigieron errores de escritura en los comandos de comprobación del hostname.
  - Se observó un error de actualización de paquetes pendiente de revisión.
  - Queda pendiente revisar la resolución del hostname respecto a la IP de gestión.
- **Siguiente paso:**
  - Completar las comprobaciones del nodo y preparar las máquinas virtuales Ubuntu Server sin entorno gráfico.
