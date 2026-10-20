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
