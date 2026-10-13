# Sesión 2 — Virtualización Profesional con Proxmox y VMware

> **Duración:** 2 horas (20:00–22:00)
> **Modalidad:** Virtual
> **Práctica virtual:** Diseño de un servidor virtualizado para servicios empresariales

---

## 1. Información general y objetivos

### Objetivos de aprendizaje

Al terminar esta sesión, el participante será capaz de:

1. Explicar qué es un **hypervisor** y diferenciar los **tipos 1 y 2**.
2. Dimensionar **CPU, RAM y almacenamiento** de máquinas virtuales con criterio profesional.
3. Diseñar **redes virtuales** (bridges, vSwitch, VLAN, bonding) para entornos virtualizados.
4. Decidir correctamente entre **snapshots** y **backups**, y entender sus límites.
5. Diseñar **clústeres con alta disponibilidad** y requisitos (quorum, storage compartido).
6. Comparar **Proxmox VE** y **VMware vSphere** a nivel de arquitectura.
7. Diferenciar **contenedores** de máquinas virtuales e introducir **Docker**.

### Contenidos cubiertos

Hypervisores · Máquinas virtuales · CPU, RAM y almacenamiento · Redes virtuales · Virtual
Switching · Snapshots · Backups · Clústeres · Alta disponibilidad · Proxmox VE · VMware ·
Introducción a contenedores · Docker.

---

## 2. Contenido teórico

### 2.1 Hypervisores

Un **hypervisor** (VMM) abstrae el hardware y permite ejecutar múltiples **máquinas
virtuales** aisladas sobre un mismo host físico.

| Tipo | Nombre | Dónde corre | Ejemplos |
| --- | --- | --- | --- |
| **Tipo 1** (bare-metal) | Nativo | Directo sobre hardware | **VMware ESXi**, **Proxmox VE (KVM)**, Hyper-V, Xen |
| **Tipo 2** (hosted) | Aplicación | Sobre un SO anfitrión | VirtualBox, VMware Workstation, QEMU en desktop |

En empresa se usa **Tipo 1**. Proxmox VE combina **KVM** (virtualización completa) + **LXC**
(contenedores) + herramientas de gestión web.

### 2.2 Máquinas virtuales: CPU, RAM y almacenamiento

**Anatomía de una VM:** vCPU, vRAM, disco virtual (vDisk), vNIC, y configuración de hardware.

- **vCPU:** asignar según núcleos reales disponibles. El **overcommit** (más vCPU que núcleos
  físicos) es posible pero degrada rendimiento si se abusa. Requiere **VT-x/AMD-V**.
- **vRAM:** cuidado con el overcommit; usar **ballooning** solo con criterio.
- **NUMA:** en hosts multi-socket, mantener una VM dentro de un nodo NUMA si es crítica.
- **Storage virtual:**
  - Local: **LVM**, **ZFS**, directorios.
  - Compartido: **NFS**, **iSCSI**, **Ceph** (hiperconvergente).
  - Formatos: **qcow2** (Proxmox), **VMDK** (VMware), **raw**.
- **Discos:** virtio (paravirtualizado, mejor rendimiento), SCSI, SATA, IDE.

**Regla de dimensionamiento:** dejar **headroom** del 20–30 % de CPU/RAM en cada host para
picos y para HA (si un nodo cae, sus VMs deben caber en los demás).

### 2.3 Redes virtuales y Virtual Switching

- **Bridge Linux** (Proxmox): `vmbr0` conecta VMs a la red física.
- **vSwitch** (VMware): virtual switch estándar o **Distributed Switch (vDS)**.
- **VLAN:** etiquetado 802.1Q para separar tráfico (producción, storage, backup, gestión).
- **Bonding / LACP:** agregación de NICs para redundancia y ancho de banda.

```
[ NIC física 1 ]──┐
                  ├── bond0 ── vmbr0 (bridge) ──┬── VM1 (VLAN 10)
[ NIC física 2 ]──┘                              ├── VM2 (VLAN 20)
                                                 └── VM3 (VLAN 30)
```

### 2.4 Snapshots

- Un **snapshot** captura el estado de una VM en un instante (disco y a veces RAM).
- Permite **revertir** rápidamente antes de un cambio riesgoso (parche, upgrade).
- **Límites:** consume espacio, degrada el rendimiento, **NO reemplaza un backup**, y depende
  del mismo almacenamiento (si el storage muere, el snapshot muere con él).

### 2.5 Backups

| Tipo | Descripción |
| --- | --- |
| Completo (full) | Copia total; lento, ocupa más. |
| Incremental | Solo cambios desde el último backup. |
| Diferencial | Cambios desde el último **full**. |

Herramientas: **Proxmox Backup Server (PBS)**, `vzdump`, **Veeam**, snapshots de storage.
Regla **3-2-1**: **3** copias, en **2** medios distintos, **1** fuera de sitio.

### 2.6 Clústeres y alta disponibilidad

- **Clúster:** varios nodos administrados como uno solo.
- **Quorum:** mayoría de nodos votantes para evitar **split-brain**. En Proxmox usa
  **Corosync**. Con 2 nodos conviene un **QDevice**.
- **Storage compartido** es requisito para **HA** (la VM debe poder arrancar en otro nodo):
  Ceph, NFS, iSCSI.
- **HA:** si un nodo cae, sus VMs se reinician en otro. En VMware: **vSphere HA** (failover) y
  **DRS** (balanceo); en Proxmox: **HA Manager**.
- **Affinity/Anti-affinity:** reglas para mantener o separar VMs (p. ej. dos controladores de
  dominio nunca en el mismo nodo).

### 2.7 Proxmox VE vs VMware

| Aspecto | **Proxmox VE** | **VMware vSphere** |
| --- | --- | --- |
| Licencia | Open source (suscripción opcional) | Comercial |
| Hypervisor | KVM + LXC | ESXi (VMkernel) |
| Gestión | Web UI + API | vCenter Server |
| Contenedores | LXC nativo | Tanzu/Kubernetes aparte |
| Storage | LVM, ZFS, Ceph, NFS, iSCSI | VMFS, vSAN, NFS, iSCSI |
| Backup | vzdump / PBS | Veeam, snapshots, SRM |
| Costo | Bajo | Alto |

Ambos son **Type 1** y soportan HA, clústeres y APIs. La elección suele ser **costo vs
soporte empresarial**.

### 2.8 Introducción a contenedores y Docker

**Contenedor:** empaqueta una aplicación con sus dependencias, compartiendo el **kernel** del
host. Es **más liviano** que una VM (no virtualiza hardware, virtualiza a nivel de SO).

```
VM:          [ App ][ Bins/Libs ][ Guest OS ]  ← hypervisor ← hardware
Contenedor:  [ App ][ Bins/Libs ] ← engine (Docker) ← SO host ← hardware
```

**Docker — conceptos clave:**

- **Imagen:** plantilla inmutable (`nginx`, `postgres`).
- **Contenedor:** instancia en ejecución de una imagen.
- **Dockerfile:** receta para construir una imagen.
- **Volumen:** persistencia de datos.
- **Red Docker:** bridge, host, overlay.

```dockerfile
# Ejemplo de Dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["python", "app.py"]
```

```bash
docker build -t miapp:1.0 .        # construir imagen
docker run -d -p 8000:8000 miapp:1.0  # ejecutar contenedor
docker ps                          # listar contenedores
docker logs <id>                   # ver logs
```

> **VM vs contenedor:** VM = aislamiento fuerte, cualquier SO; contenedor = ligero, rápido,
> mismo SO/kernel. En la práctica **se combinan**: contenedores dentro de VMs.

---

## 3. Práctica virtual guiada — Diseño de un servidor virtualizado empresarial

**Escenario:** *"ServiciosAndinos"* debe consolidar 8 servidores físicos antiguos en **un
host virtualizado** (que luego crecerá a clúster). Servicios: 1 DC, 1 file server, 1 base de
datos, 2 web, 1 aplicación ERP, 1 correo, 1 servidor de backup.

### Paso 1 — Inventario y consolidación

| Servicio | vCPU | vRAM | Disco | Notas |
| --- | --- | --- | --- | --- |
| Controlador de dominio | 2 | 6 GB | 80 GB | |
| File server | 2 | 4 GB | 500 GB | NAS/volumen |
| Base de datos | 4 | 16 GB | 300 GB | RAID 10 (disco) |
| Web 1 | 2 | 4 GB | 60 GB | |
| Web 2 | 2 | 4 GB | 60 GB | |
| ERP | 4 | 12 GB | 200 GB | Crítico |
| Correo | 2 | 8 GB | 150 GB | |
| Backup | 2 | 4 GB | 100 GB | PBS aparte |
| **Total** | **20** | **58 GB** | **1.450 GB** | |

### Paso 2 — Dimensionar el host

- **CPU:** 20 vCPU + headroom 30 % → **≈ 26 vCPU** → usar **2 sockets × 16 cores = 32 núcleos**.
- **RAM:** 58 GB + headroom 30 % → **≈ 76 GB** → usar **96–128 GB** (ECC).
- **Storage:** 1.450 GB + crecimiento → **2 TB útiles** en **SSD/NVMe RAID 10** o **Ceph**.
- **Red:** 2 × 25 GbE en **bond** (LACP) + VMware/Proxmox con VLANs.

### Paso 3 — Plataforma

- Elegir **Proxmox VE** (justificar por costo) o **VMware** (justificar por soporte).
- Instalar sobre el host, configurar **vmbr0** con bond, y crear **pool** de almacenamiento.

### Paso 4 — Redes y VLANs

```
VLAN 10 → Producción (web, ERP)
VLAN 20 → Datos/DB
VLAN 30 → Gestión/Backup
VLAN 40 → Storage
```

### Paso 5 — HA, snapshots y backups

- Proyectar **clúster de 3 nodos** con **Ceph** para storage compartido y HA.
- Política: **snapshot** antes de cada cambio; **backup diario incremental** a PBS;
  regla **3-2-1** con copia fuera de sitio.

### Entregable

Diagrama en draw.io con: host físico, VMs, bridges/bond, VLANs, y un cuadro de HA/backup.
Incluir la tabla de dimensionamiento y la justificación de plataforma.

![Diagrama base de la práctica 2](practicas/sesion-02-servidor-virtualizado.png)

> **Plantilla base editable:** [`practicas/sesion-02-servidor-virtualizado.drawio`](practicas/sesion-02-servidor-virtualizado.drawio)
> · vectorial [`SVG`](practicas/sesion-02-servidor-virtualizado.svg)
> · abrir en draw.io / diagrams.net y completar.

---

## 4. Ejercicios propuestos

**Ejercicio 1.** Un host tiene 128 GB de RAM y ya usa 96 GB. ¿Puedo crear una VM de 40 GB sin
riesgo? ¿Por qué?

**Ejercicio 2.** Diferencia entre **snapshot** y **backup**. Da un caso donde usar cada uno.

**Ejercicio 3.** Tienes 2 nodos y quieres HA. ¿Qué problema aparece y cómo se resuelve?

**Ejercicio 4.** Escribe los comandos Docker para construir una imagen llamada `api:2.0` y
correrla en el puerto 8080.

**Ejercicio 5.** ¿Cuándo usarías un contenedor en vez de una VM? Da 2 ejemplos y 1 contraejemplo.

---

## 5. Solucionario

**Solución 1.** No conviene. `96 + 40 = 136 GB > 128 GB` → **no hay RAM física suficiente**.
Aunque exista overcommit/ballooning, compromete rendimiento y rompe la premisa de HA. Opciones:
reducir la VM, ampliar RAM del host o mover otra VM a otro nodo.

**Solución 2.** **Snapshot** = estado revertible rápido en el mismo storage, para cambios
riesgosos y a corto plazo (ej.: *antes de aplicar un parche al ERP*). **Backup** = copia
independiente, para recuperación ante desastres y retención (ej.: *restaurar la base de datos
de ayer si hubo corrupción*). El snapshot **no protege** si el storage falla.

**Solución 3.** Con 2 nodos no se alcanza **quorum** (empate 1-1) y aparece **split-brain**.
Soluciones: agregar un **tercer nodo/QDevice (corosync-qdevice)** o un **testigo** (witness),
de modo que siempre haya mayoría impar.

**Solución 4.**
```bash
docker build -t api:2.0 .
docker run -d -p 8080:8080 api:2.0
```

**Solución 5.**
- **Contenedor:** microservicio stateless (una API REST) o un worker de cola; arranque rápido,
  escalado horizontal, mismo kernel Linux.
- **Contraejemplo:** una base de datos con requisitos de aislamiento y kernel específico o
  drivers de hardware → mejor **VM** (o contenedor con cuidado y almacenamiento persistente
  bien diseñado). Regla: contenedor para **apps**, VM para **SO completo/aislamiento fuerte**.

---

## 6. Recursos y lecturas sugeridas

- Documentación oficial de **Proxmox VE** (pve.proxmox.com).
- **VMware** — vSphere documentation.
- **Docker** — docs.docker.com, "Get Started".
- **Ceph** — arquitectura de almacenamiento distribuido.
- **Proxmox Backup Server** — guía de backups 3-2-1.

---

## 7. Autoevaluación (quiz)

1. ¿Tipo 1 o Tipo 2? ESXi y Proxmox VE.
2. ¿Qué es el **overcommit** y cuándo es peligroso?
3. Nombra 3 tipos de storage compartido usados para HA.
4. ¿Por qué un snapshot no reemplaza un backup?
5. Diferencia clave entre **VM** y **contenedor**.

<details>
<summary>Respuestas</summary>

1. Ambos son **Tipo 1** (bare-metal).
2. Asignar más recursos virtuales que físicos; peligroso con RAM y con vCPU bajo carga.
3. Ceph, NFS, iSCSI (también vSAN/VMFS).
4. Vive en el mismo storage que la VM; si el storage falla, se pierden ambos.
5. La VM virtualiza hardware (kernel propio); el contenedor comparte el kernel del host.

</details>

---

## 8. Glosario de la sesión

| Término | Definición breve |
| --- | --- |
| **Hypervisor** | Software que ejecuta y aísla máquinas virtuales. |
| **vCPU/vRAM/vDisk/vNIC** | Recursos virtuales de una VM. |
| **Overcommit** | Asignar más recursos virtuales que físicos. |
| **Ballooning** | Recuperación dinámica de RAM del host. |
| **Bonding/LACP** | Agregación de enlaces de red. |
| **Snapshot** | Estado puntual revertible de una VM. |
| **vzdump / PBS** | Herramienta de backup de Proxmox / servidor de backups. |
| **Quorum** | Mayoría de nodos que evita split-brain. |
| **Ceph** | Storage distribuido hiperconvergente. |
| **LXC** | Contenedores de sistema en Proxmox. |
| **Docker** | Plataforma de contenedores de aplicación. |
