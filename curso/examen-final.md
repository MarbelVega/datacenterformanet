# Examen Final Integrador — Data Center, Cloud, Virtualización y Redes Definidas por Software

> **Duración:** 90 minutos
> **Puntaje total:** 100 puntos
> **Modalidad:** Individual · calculadora permitida · sin apuntes
> **Cobertura:** Sesiones 1 a 5 (integrador)

---

## Instrucciones

1. Responda todas las secciones. El puntaje por ítem figura en cada encabezado.
2. En la Parte B **justifique siempre** la respuesta; sin justificación no se otorga puntaje.
3. En los cálculos, **muestre el procedimiento** (fórmula y números intermedios).
4. Administre el tiempo: Parte A ≈ 20 min · B ≈ 15 min · C ≈ 20 min · D ≈ 35 min.

| Parte | Tipo | Ítems | Puntos |
| --- | --- | --- | --- |
| A | Selección múltiple | 10 × 3 | 30 |
| B | Verdadero / Falso con justificación | 5 × 4 | 20 |
| C | Ejercicios de cálculo | 5 × 4 | 20 |
| D | Caso integrador (diseño) | 1 × 30 | 30 |
| | | | **100** |

---

## Parte A — Selección múltiple (10 ítems × 3 pts = 30)

Marque la **única** opción correcta.

**A1.** (S1) El nivel Tier que garantiza **mantenimiento concurrente** pero que una falla
**sí** puede interrumpir la carga es:

- a) Tier I  b) Tier II  c) **Tier III**  d) Tier IV

**A2.** (S1) El nivel RAID que tolera la falla simultánea de **dos** discos es:

- a) RAID 0  b) RAID 1  c) RAID 5  d) **RAID 6**

**A3.** (S2) ¿Cuál de los siguientes es un hypervisor **Tipo 1** (bare-metal)?

- a) VirtualBox  b) VMware Workstation  c) **VMware ESXi**  d) QEMU sobre escritorio

**A4.** (S2) Respecto a los **snapshots**, la afirmación correcta es:

- a) Reemplazan completamente a los backups
- b) **No reemplazan un backup y viven en el mismo storage**
- c) Sobreviven a la pérdida total del storage
- d) No consumen espacio ni afectan el rendimiento

**A5.** (S2) El requisito esencial para poder hacer **HA** de máquinas virtuales en un clúster es:

- a) Un solo nodo muy potente
- b) **Storage compartido accesible por todos los nodos**
- c) Usar únicamente discos locales
- d) Desactivar el quorum

**A6.** (S3) El componente que permite que una **subred privada** en AWS acceda a Internet
para actualizaciones **sin** recibir conexiones entrantes es:

- a) Internet Gateway  b) **NAT Gateway**  c) VPC Peering  d) Transit Gateway

**A7.** (S3) El firewall **stateful** a nivel de **instancia** en AWS es:

- a) NACL  b) **Security Group**  c) Route Table  d) IGW

**A8.** (S4) Una aplicación que necesita pedir una política al controlador SDN utiliza la
interfaz:

- a) Southbound  b) **Northbound (REST)**  c) East/West  d) Data Plane

**A9.** (S4) **NFV** virtualiza fundamentalmente:

- a) Los switches de la red física
- b) **Las funciones de red (VNF) que antes eran appliances dedicados**
- c) El plano de datos del hardware
- d) El direccionamiento IP

**A10.** (S5) **SASE** se compone de la unión de:

- a) MPLS + VPN
- b) **SD-WAN + SSE**
- c) VLAN + NACL
- d) ZTNA + MPLS

---

## Parte B — Verdadero / Falso con justificación (5 ítems × 4 pts = 20)

Indique **V** o **F** y justifique en 1–2 líneas.

**B1.** RAID 5 tolera la falla simultánea de dos discos.

**B2.** Una UPS **online (doble conversión)** es la tecnología más adecuada para un Data
Center crítico.

**B3.** En SDN, el **plano de control** calcula las rutas y el **plano de datos** ejecuta el
reenvío de los paquetes.

**B4.** En AWS, la **NACL** es *stateful* y el **Security Group** es *stateless*.

**B5.** **ZTNA** otorga acceso a **toda la red** una vez autenticado el usuario, igual que una
VPN tradicional.

---

## Parte C — Ejercicios de cálculo (5 ítems × 4 pts = 20)

Muestre el procedimiento.

**C1. (S1) PUE.** Un Data Center consume **150 kW** en total y la carga de TI es de **100 kW**.
Calcule el PUE e indique si es eficiente respecto al objetivo ≤ 1,4.

**C2. (S1) UPS.** La carga a proteger es de **8 kW** con factor de potencia **0,9**. Calcule la
UPS mínima en **VA** agregando un **25 %** de margen.

**C3. (S3) CIDR.** Se define una VPC con el bloque **10.30.0.0/16**. Indique cuántas
direcciones caben, la máscara de subred en notación decimal y cuántas subredes **/24** se
pueden obtener.

**C4. (S1) Disponibilidad.** Un servicio registró **45 minutos** de caída en un año. Calcule su
disponibilidad y exprésela en porcentaje y en "nueves" aproximados.

**C5. (S2) Dimensionamiento.** Un host virtualizará VMs con este consumo: vCPU =
2+2+4+2+2+4 y RAM = 6+4+16+4+4+12 GB. Calcule el **total** y el **host recomendado**
aplicando un headroom del **30 %**.

---

## Parte D — Caso integrador (30 pts)

**Escenario — "SaludTotal"** es una red de clínicas con **dos sedes** (Central y Sucursal),
que quiere modernizar su infraestructura. Tiene un servidor físico antiguo por sede, quiere
crecer a la nube para su portal de pacientes y necesita conectar ambas sedes de forma segura.
Presupuesto moderado; prioridad en **disponibilidad y seguridad**.

Diseñe **una solución integradora** que cubra, en un documento breve:

1. **Sede Central (S1):** esquema de energía/clima con redundancia **N+1**, racks necesarios y
   Tier objetivo. Justifique la redundancia elegida.
2. **Virtualización (S2):** plataforma sugerida (Proxmox o VMware), dimensionamiento del host,
   y política de **HA + backups**.
3. **Nube híbrida (S3):** proponga una **VPC/VNet** con subredes públicas y privadas,
   conectividad **híbrida** y un balanceador; indique cómo asegura la conectividad y el backup.
4. **Red del Data Center (S4):** describa en **una frase** qué elementos serían **SDN** y cuáles
   **NFV** en su diseño.
5. **Multisede (S5):** cómo conecta **ambas sedes** con **SD-WAN** y protege el acceso remoto
   con **ZTNA/SASE**; incluya **una política** para el tráfico de la aplicación crítica.

Se evalúa la **coherencia** entre las cinco capas, la **justificación técnica** y el uso de
estándares (Tier, N+1, 3-2-1, CIDR sin solape, Zero Trust).

---

# Solucionario

## Clave rápida — Parte A

| Ítem | Resp. | Ítem | Resp. |
| --- | --- | --- | --- |
| A1 | c | A6 | b |
| A2 | d | A7 | b |
| A3 | c | A8 | b |
| A4 | b | A9 | b |
| A5 | b | A10 | b |

---

## Parte A — Justificaciones

- **A1 (c) Tier III.** Redundancia **N+1** con mantenimiento concurrente, pero *no* tolera
  una falla sin interrumpir la carga. Tier IV sí (2N/2N+1).
- **A2 (d) RAID 6.** Doble paridad → tolera 2 fallos. RAID 5 solo 1.
- **A3 (c) ESXi.** Hypervisor Tipo 1 bare-metal (igual que Proxmox VE/KVM). Los demás son Tipo 2.
- **A4 (b).** El snapshot vive en el **mismo storage**: si el storage cae, se pierde. No es backup.
- **A5 (b) Storage compartido.** LA VM debe poder arrancar en otro nodo: necesita Ceph/NFS/iSCSI.
- **A6 (b) NAT Gateway.** Salida a Internet sin conexiones entrantes.
- **A7 (b) Security Group.** *Stateful*, a nivel instancia. La NACL es *stateless*, a nivel subred.
- **A8 (b) Northbound (REST).** La app consume el controlador por la interfaz norte.
- **A9 (b) Funciones de red (VNF).** NFV convierte appliances en software; SDN las controla.
- **A10 (b) SD-WAN + SSE.** SASE = conectividad (SD-WAN) + seguridad (SSE).

---

## Parte B — Respuestas y justificación

- **B1 — FALSO.** RAID 5 tolera **1** disco; para 2 fallos se necesita RAID 6.
- **B2 — VERDADERO.** La online (doble conversión) entrega energía limpia y constante sin
  conmutación perceptible, ideal para cargas críticas.
- **B3 — VERDADERO.** Es la esencia de SDN: control centralizado (decide) y datos distribuidos
  (reenvían).
- **B4 — FALSO.** Es al revés: el **Security Group es stateful** y la **NACL es stateless**.
- **B5 — FALSO.** ZTNA aplica **Zero Trust** y da acceso **por recurso/aplicación**, no a toda
  la red (eso es justamente el riesgo de la VPN).

---

## Parte C — Cálculos

**C1. PUE**
`PUE = 150 kW / 100 kW = 1,5`.
**No es eficiente** respecto al objetivo ≤ 1,4 → hay margen de mejora (contención de pasillos,
elevar temperatura dentro del rango ASHRAE, apagado de zonas frías, revisión de climatización).

**C2. UPS en VA**
- `VA = W / FP = 8.000 / 0,9 ≈ 8.888,9 VA`.
- Con margen 25 %: `8.888,9 × 1,25 ≈ 11.111 VA`.
- **UPS mínima ≈ 11,1 kVA** → en catálogo comercial, elegir el modelo **≥ 15 kVA**.

**C3. CIDR**
- Direcciones: `2^(32−16) = 2^16 = 65.536`.
- Máscara decimal: **255.255.0.0**.
- Subredes /24: `2^(24−16) = 2^8 = 256` subredes de `/24`.

**C4. Disponibilidad**
- Minutos en un año: `365 × 24 × 60 = 525.600`.
- `Disponibilidad = 1 − (45 / 525.600) = 0,99991438 → 99,9914 %`.
- Está **entre 4 nueves (52,6 min/año) y 5 nueves (5,26 min/año)** → ≈ **4,9 nueves**.
  (Con 45 min al año queda por debajo de 5 nueves.)

**C5. Dimensionamiento**
- vCPU total: `2+2+4+2+2+4 = 16`.
- RAM total: `6+4+16+4+4+12 = 46 GB`.
- Con **+30 %** de headroom:
  - vCPU: `16 × 1,3 ≈ 20,8` → host con **≥ 24 núcleos** (p. ej. 2 × 16 = 32 para crecer/HA).
  - RAM: `46 × 1,3 ≈ 59,8 GB` → host de **64–96 GB ECC** (96 GB recomendado si se prevé HA).
- Recordar: el headroom también debe permitir **absorber las VMs de otro nodo** ante failover.

---

## Parte D — Solución modelo (referencia para corrección)

**D1. Sede Central (S1).**
- Energía y clima en **N+1** (una UPS extra, un CRAC extra, generador con ATS), acometidas
  redundantes si el edificio lo permite.
- Tier objetivo **III** (mantenimiento concurrente, SLA ~99,982 %): buen equilibrio costo/SLA.
- Racks: contar servidores + red + storage y dejar **≈ 30 % libre**; 2–4 racks de 42U.
- Justificación: N+1 da tolerancia a un fallo con costo razonable; 2N completo se descarta por
  presupuesto moderado.

**D2. Virtualización (S2).**
- Plataforma: **Proxmox VE** (menor costo) o VMware (si prioriza soporte). Host con CPU ≥ 24
  núcleos, **96 GB RAM ECC**, storage **NVMe RAID 10**.
- **HA:** clúster de mínimo **3 nodos** con **Ceph** (storage compartido) y **quorum**.
- **Backups:** **Proxmox Backup Server / Veeam**, diarios incrementales, regla **3-2-1**.
- **Snapshots** solo para cambios puntuales, nunca como backup.

**D3. Nube híbrida (S3).**
- **VPC 10.10.0.0/16** (sin solape con on-premise, p. ej. `10.0.0.0/16`), con subredes
  **públicas** (ALB/IGW) y **privadas** (app/DB) en **2 AZ**.
- Conectividad híbrida: **VPN Site-to-Site (2 túneles)** o **Direct Connect** si el volumen lo
  justifica; **NAT Gateway** para salida de subredes privadas.
- **Balanceador:** **ALB** (HTTPS/WAF) hacia la capa de aplicación.
- **Seguridad:** Security Groups restrictivos (ALB→app:443, app→DB:5432), IAM mínimo
  privilegio + MFA, cifrado en reposo/tránsito.
- **Backup a cloud:** **S3** cifrado y versionado vía el túnel.

**D4. Red del DC (S4).**
- **SDN:** el **controlador central** separa control de datos y programa los **switches** de
  la sede (políticas por API).
- **NFV:** el **firewall, el balanceador y la VPN** pueden desplegarse como **VNF** (software)
  en el NFVI en lugar de appliances dedicados.

**D5. Multisede (S5).**
- **SD-WAN:** edges en ambas sedes con **overlay IPsec** sobre **múltiples transportes**
  (fibra + 4G/5G) y **selección dinámica de enlaces** por **probes de SLA**; **local breakout**
  para SaaS (Microsoft 365) y conectividad a la nube.
- **Seguridad:** **SASE/SSE** con **FWaaS**, **SWG** y **ZTNA** para usuarios remotos
  (Zero Trust, acceso por recurso, sin exponer la red interna).
- **Política crítica (ejemplo):** *"El tráfico del portal de pacientes se prioriza sobre el
  enlace con menor latencia que cumpla el SLA; ante degradación, failover inmediato al enlace
  secundario; en horario nocturno, los backups usan el enlace de bajo costo."*

### Rúbrica de la Parte D (30 pts)

| Criterio | Puntos |
| --- | --- |
| S1: redundancia N+1, racks y Tier justificados | 6 |
| S2: plataforma, dimensionamiento y HA/backups coherentes | 6 |
| S3: VPC/subredes sin solape, conectividad híbrida y seguridad | 6 |
| S4: identificación correcta de SDN vs NFV | 4 |
| S5: SD-WAN + SASE/ZTNA y política concreta | 6 |
| Coherencia global entre las 5 capas | 2 |
| | **30** |

---

## Tabla de referencia rápida (fórmulas)

| Magnitud | Fórmula |
| --- | --- |
| PUE | `Energía total / Energía de TI` |
| UPS en VA | `VA = W / FP` (luego + margen) |
| Direcciones de un prefijo | `2^(32 − máscara)` |
| Subredes de tamaño k en un prefijo p | `2^(k − p)` |
| Disponibilidad | `1 − (tiempo de caída / tiempo total)` |
| Headroom de host | `consumo × (1 + %)` |
