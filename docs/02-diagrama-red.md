
# 02 - Diseño y diagrama de red

## Objetivo

Se diseña la infraestructura de red del laboratorio Proxmox para permitir la administración del nodo, la comunicación entre máquinas virtuales y el acceso controlado a Internet.

Se utiliza VirtualBox como plataforma anfitriona para ejecutar Proxmox VE 9.2.2, separando la conexión externa de la red interna del laboratorio.

## Procedimiento

### 1. Topología de red

Se utilizan dos adaptadores virtuales en VirtualBox:

- **Adaptador 1 (NAT):** proporciona conectividad externa al nodo Proxmox.
- **Adaptador 2 (Solo-anfitrión):** permite la comunicación entre el ordenador anfitrión, Proxmox y las máquinas virtuales conectadas a la red interna.

Diagrama de conexiones:

```text
                  INTERNET
                     |
               NAT VirtualBox
                     |
               10.0.2.0/24
                     |
               vmbr0 (PVE)
               10.0.2.15
                     |
            +----------------+
            |  pve-aljarilla |
            |  Proxmox VE    |
            |  192.168.56.10 |
            +----------------+
                     |
               vmbr1 (PVE)
                     |
             192.168.56.0/24
                     |
          +----------+----------+
          |                     |
    Linux anfitrión          VM srv-aso
    192.168.56.1             192.168.56.20*
                             (IP prevista)

* La dirección del servidor está pendiente
  de configurar y verificar tras reinstalar Ubuntu.
```

### 2. Direccionamiento IP

| Equipo o interfaz | Dirección IP | Función |
|---|---|---|
| Gateway NAT VirtualBox | 10.0.2.2 | Salida externa |
| Proxmox - vmbr0 | 10.0.2.15/24 | Conexión NAT |
| Proxmox - vmbr1 | 192.168.56.10/24 | Gestión y red interna |
| Anfitrión - Host-Only | 192.168.56.1/24 | Acceso desde el aula |
| srv-aso | 192.168.56.20/24 (prevista) | Servidor del proyecto |
| cli-linux | Pendiente de definir | Cliente Linux |

La puerta de enlace principal del nodo Proxmox es `10.0.2.2`, a través de `vmbr0`.

Para `srv-aso` se ha previsto utilizar `192.168.56.10` como puerta de enlace, una vez validado el enrutamiento desde la máquina virtual.

El servicio DNS de las máquinas invitadas queda pendiente de comprobar.

### 3. Configuración de los puentes

Se configuran dos puentes de red:

**vmbr0:**
- Conectado al adaptador NAT de VirtualBox.
- Dirección IPv4: `10.0.2.15/24`.
- Gateway: `10.0.2.2`.
- Permite la salida externa del nodo.

**vmbr1:**
- Conectado al adaptador Solo-anfitrión.
- Dirección IPv4: `192.168.56.10/24`.
- Permite gestionar Proxmox desde el equipo físico.
- Proporciona conectividad interna a las máquinas virtuales.

### 4. Puertos y servicios

| Servicio | Puerto | Utilidad |
|---|---|---|
| HTTPS Proxmox | TCP 8006 | Administración web |
| SSH | TCP 22 | Administración por terminal |
| DNS | TCP/UDP 53 | Resolución de nombres, cuando se configure |
| HTTP | TCP 80 | Posibles pruebas de servicios web |
| HTTPS | TCP 443 | Posibles pruebas de servicios seguros |

Los puertos de servicios futuros no implican que dichos servicios estén instalados o habilitados actualmente.

### 5. Seguridad y aislamiento

Se emplea una red Solo-anfitrión para mantener separado el tráfico de laboratorio de la red física del centro.

Se mantiene desactivado el servidor DHCP de VirtualBox en esta red, con el objetivo de utilizar direccionamiento estático.

El acceso a la administración web se realiza mediante HTTPS y se utiliza la autenticación administrativa de Proxmox.

Se ha probado una configuración temporal de reenvío IPv4 y una regla de NAT MASQUERADE para permitir la salida de las máquinas invitadas.

Esta configuración no se ha establecido como persistente y su funcionamiento desde Ubuntu Server está pendiente de validar.

## Comandos usados

Se utilizan los siguientes comandos para comprobar la configuración del nodo:

```bash
ip -br addr
ip route
hostname -f
getent hosts "$(hostname -f)"
```

Para revisar el reenvío y las reglas NAT:

```bash
sysctl net.ipv4.ip_forward
iptables -t nat -S
```

## Evidencias

Se dispone de evidencias anteriores de:

- Configuración de puentes de red.
- Acceso web a Proxmox.
- Validación de interfaces y rutas.
- Resolución del nombre del nodo.
- Configuración temporal de NAT.

Las capturas se incorporarán y referenciarán en el repositorio durante la revisión final.

## Incidencias

Durante la preparación se detectaron diferencias entre el direccionamiento inicial y la configuración definitiva de los adaptadores de VirtualBox.

Se revisaron las interfaces, las rutas y la resolución del hostname, dejando operativa la gestión de Proxmox mediante `192.168.56.10`.

Tras reinstalar Ubuntu Server, queda pendiente configurar y verificar la dirección IPv4 del servidor.

## Conclusión

Se ha diseñado y configurado una topología con dos redes diferenciadas: una destinada a la conectividad externa y otra a la gestión y comunicación interna del laboratorio.

El acceso web al nodo Proxmox se ha comprobado correctamente.

Queda pendiente validar la conectividad completa de las máquinas invitadas y documentar las pruebas finales.

[Volver al índice](../README.md)
