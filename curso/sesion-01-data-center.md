# Sesión 1 — Data Center Profesional: Diseño, Energía, Redundancia y Alta Disponibilidad

> **Duración:** 2 horas (20:00–22:00)
> **Modalidad:** Virtual
> **Práctica virtual:** Diseño conceptual de un Data Center empresarial

---

## 1. Información general y objetivos

### Objetivos de aprendizaje

Al terminar esta sesión, el participante será capaz de:

1. Describir la **arquitectura general de un Data Center** y los subsistemas que lo componen.
2. Diferenciar elementos de **cómputo, almacenamiento, red y facilities** (energía, clima, seguridad).
3. Explicar los modelos de **redundancia N, N+1, 2N y 2N+1** y su impacto en disponibilidad.
4. Clasificar un Data Center según los **niveles Tier I–IV** del Uptime Institute.
5. Calcular de forma básica **potencia, autonomía de UPS, generación y carga térmica (PUE)**.
6. Diseñar **conceptualmente** un Data Center empresarial justificando cada decisión.

### Contenidos cubiertos

Arquitectura de Data Center · Racks · Servidores · Storage · Networking · UPS · Generadores ·
Climatización · N+1 / 2N · Seguridad física · Monitoreo ambiental · Infraestructura crítica ·
Alta disponibilidad · Niveles Tier.

---

## 2. Contenido teórico

### 2.1 ¿Qué es un Data Center?

Un **Data Center (DC)** es una instalación que concentra **cómputo, almacenamiento y red**
junto con la **infraestructura de soporte** (energía, climatización, seguridad y monitoreo)
necesaria para operar servicios de TI de forma **continua, segura y confiable**.

Se puede pensar como un edificio dentro de un edificio: la sala de TI es el corazón, pero sin
un buen subsistema eléctrico y térmico, el corazón se detiene.

**Tipos de Data Center:**

| Tipo | Descripción | Uso típico |
| --- | --- | --- |
| Empresarial (on-premise) | Propiedad de la empresa, uso privado | Corporativo, banca, salud |
| Colocation | Espacio alquilado en un DC de terceros | Empresas sin sitio propio |
| Gestionado (managed) | Tercero opera la infraestructura | Pymes, servicios gestionados |
| Hyperscale | Escala masiva, diseño propio | AWS, Azure, Google |
| Edge | Pequeño, cerca del usuario | IoT, 5G, CDN, retails |

### 2.2 Arquitectura de Data Center

La arquitectura se organiza en **capas físicas y lógicas**:

```
┌──────────────────────────────────────────────────────┐
│  Servicios (apps, DB, web, backup)                    │
├──────────────────────────────────────────────────────┤
│  Cómputo        │  Storage        │  Networking       │
│  (servidores)   │  (SAN/NAS/DAS)  │  (switches/rt)    │
├──────────────────────────────────────────────────────┤
│  Racks y cableado estructurado (cobre/fibra)          │
├──────────────────────────────────────────────────────┤
│  Facilities: energía │ climatización │ seguridad      │
└──────────────────────────────────────────────────────┘
```

**Topologías de red interna más comunes:**

- **Tres capas (Core–Aggregation–Access):** clásica, escalable en Norte-Sur.
- **Spine–Leaf (hoja–espina):** estándar moderno en DC; cada leaf se conecta a cada spine,
  todo el tráfico es Este-Oeste y de latencia predecible.

**Ubicación de switches:**

- **ToR (Top of Rack):** switch arriba de cada rack — cableado corto, fácil crecimiento.
- **EoR (End of Rack):** al final de la fila — menos switches, más cable.
- **MoR (Middle of Rack):** compromiso entre ambos.

### 2.3 Racks

El **rack** es el mueble estandarizado donde se montan servidores y equipos de red.

- Unidad de medida: **U (Rack Unit) = 1,75 pulgadas = 44,45 mm de alto**.
- Altura típica: **42U** (también 45U, 48U). Ancho 19" (EIA-310).
- Buenas prácticas: **numerar cada U**, dejar **1U ciego cada 2–3U** para temperatura,
  ordenar el **cableado frontal y trasero** por separado, usar **PDU** (unidad de distribución
  de energía) y **organizadores de cable**.

**Densidad y potencia por rack:** un rack moderno puede pasar de 3–5 kW (tradicional) a
**10–20 kW** (contenedores/NVMe). Eso cambia el diseño térmico y eléctrico.

### 2.4 Servidores

**Formatos:**

| Formato | Descripción | Ventaja |
| --- | --- | --- |
| Rack (1U/2U/4U) | Montaje en rack estándar | Escalable, ordenado |
| Blade | Chasis compartido | Densidad y cableado reducido |
| Torre | Independiente | Pymes, oficina |
| High-density / GPU | 4U+ con GPUs | IA/ML, HPC |

**Componentes clave:**

- **CPU:** núcleos, threads, frecuencia, sockets.
- **RAM:** cantidad, tipo (DDR4/DDR5), ECC.
- **Almacenamiento local:** HDD, SSD SATA, **NVMe**.
- **NIC:** 1/10/25/100 GbE, Fibre Channel para SAN.
- **BMC:** gestión fuera de banda (**IPMI**, **iDRAC** Dell, **iLO** HPE, **IPMI/BMC** genérico).
  Permite encender, reiniciar y ver consola aunque el sistema operativo esté caído.

### 2.5 Storage

**Tipos de almacenamiento:**

| Tipo | Significado | Protocolo | Escenario |
| --- | --- | --- | --- |
| DAS | Direct Attached Storage | SATA/SAS/NVMe | Local al servidor |
| NAS | Network Attached Storage | NFS/SMB | Compartición de archivos |
| SAN | Storage Area Network | iSCSI/FC/FCoE | Bloques de alto rendimiento |

**RAID — niveles habituales:**

| Nivel | Descripción | Tolerancia | Rendimiento lectura/escritura |
| --- | --- | --- | --- |
| RAID 0 | Striping | Ninguna | ▲▲ / ▲▲ |
| RAID 1 | Mirroring | 1 disco | ▲ / = |
| RAID 5 | Paridad distribuida | 1 disco | ▲▲ / ▲ |
| RAID 6 | Doble paridad | 2 discos | ▲▲ / = |
| RAID 10 | Mirror + stripe | 1 por espejo | ▲▲ / ▲▲ |

Reglas prácticas: RAID **no es backup**; para bases de datos y VMs suele preferirse **RAID 10**.

**Medios:** HDD (capacidad/barato), SSD SATA (equilibrio), **NVMe** (máximo IOPS).
**Tiers:** almacenamiento en niveles (caliente/tibio/frío) según frecuencia de acceso.

### 2.6 Networking

- **Switching** de acceso (ToR), **routing** en spine/core.
- **VLANs** para segmentar funciones (gestión, producción, storage, backup).
- **Redundancia:** stacking, **MLAG/VPC**, **bonding** de uplinks, doble spine.
- **Ancho de banda:** prever factor 2–3 sobre el consumo actual.
- **VLAN de gestión aislada** para BMC/IPMI nunca expuesta a Internet.

### 2.7 Energía: UPS

La **UPS (Uninterruptible Power Supply)** protege contra cortes y transitorios.

| Tecnología | Funcionamiento | Uso |
| --- | --- | --- |
| Offline (standby) | Conmuta al fallar | Equipos no críticos |
| Line-interactive | Regula tensión + conmuta | SMB |
| **Online (doble conversión)** | CA→CC→CA constante | Data Center crítico |

**Conceptos clave:**

- **Autonomía:** minutos que sostiene la carga (suficiente para apagar ordenadamente o
  arrancar el generador).
- **Carga (VA/W):** sumar consumo de racks, switches, storage. Factor de potencia ~0,8–0,9.
- **ATS (Automatic Transfer Switch):** conmuta entre acometida y generador.
- **PDU / PDU inteligente:** distribuYe energía al rack y permite medición por tomacorriente.

### 2.8 Generadores

- **Grupo electrógeno** (diésel/gas) para cortes prolongados.
- Secuencia típica: corte → UPS sostiene → **ATS arranca generador** → transferencia.
- Debe **probarse periódicamente** (prueba con carga). Combustible con autonomía ≥ 24–48 h.
- Contemplar **tanques de combustible** y normativa local.

### 2.9 Climatización

La energía eléctrica se transforma en calor: hay que **extraerlo**.

- **CRAC** (aire) / **CRAH** (agua fría) / **in-row** (en fila) / **in-rack**.
- **Pasillos fríos/calientes (hot/cold aisle):** orientar los equipos para que el aire frío
  entre por el frente y el caliente salga por atrás.
- **Contención** de pasillos (fríos o calientes) para evitar mezcla de aire.
- Rangos sugeridos **ASHRAE**: clase A1, 18–27 °C, humedad 8–60 % HR.
- **PUE (Power Usage Effectiveness)** = Energía total del DC / Energía de TI.
  Ideal cercano a **1,0**; 1,2 es muy bueno, 1,8–2,0 es ineficiente.

```
PUE = 1000 kW (total instalación) / 700 kW (TI) ≈ 1,43
```

### 2.10 Redundancia: N, N+1, 2N, 2N+1

| Modelo | Significado | Disponibilidad |
| --- | --- | --- |
| **N** | Capacidad exacta, sin respaldo | Menor |
| **N+1** | Un componente extra de reserva | Alta |
| **2N** | Dos sistemas completos independientes | Muy alta |
| **2N+1** | Dos completos + un extra | Máxima (caro) |

Se aplica a **todo**: UPS, generadores, climatización, rutas eléctricas y enlaces de red.

### 2.11 Seguridad física

- **Control de acceso:** tarjeta, biometría, PIN, **mantrap/portátil con doble puerta**.
- **Videovigilancia (CCTV)** con retención de grabaciones.
- **Jaulas y gabinetes** con cerradura en colocation.
- **Detección:** sensores de puerta, movimiento, vidrio.
- **Personal:** registro de visitantes y escolta.

### 2.12 Monitoreo ambiental

- Sensores de **temperatura, humedad, fuga de agua, humo, consumo eléctrico**.
- Integración vía **SNMP / Syslog / API** hacia un **DCIM** (Data Center Infrastructure
  Management) o un NMS.
- Alertas **proactivas** (umbrales) y dashboards.

### 2.13 Infraestructura crítica y alta disponibilidad

- **Infraestructura crítica:** aquel sistema cuya caída detiene el negocio. Se le asigna el
  mayor nivel de redundancia y mantenimiento.
- **Disponibilidad** = MTBF / (MTBF + MTTR). Expresada como **"nueves"**:

| Disponibilidad | Caída/año aprox. |
| --- | --- |
| 99 % (2 nueves) | 3,65 días |
| 99,9 % (3 nueves) | 8,8 horas |
| 99,99 % (4 nueves) | 52,6 min |
| 99,999 % (5 nueves) | 5,26 min |

- **Clustering y failover:** en cómputo (clústeres) y en red (rutas y enlaces redundantes).

### 2.14 Niveles Tier (Uptime Institute)

| Tier | Redundancia | Disponibilidad | Mantenimiento concurrente |
| --- | --- | --- | --- |
| **I** | N | 99,671 % | No |
| **II** | N+1 (parcial) | 99,741 % | No |
| **III** | N+1 | 99,982 % | Sí (concurrente) |
| **IV** | 2N / 2N+1 | 99,995 % | Sí (tolerante a fallos) |

La diferencia clave entre **III y IV** es que en Tier IV **una falla no interrumpe** la carga;
en Tier III el mantenimiento es concurrente pero una falla sí puede sacar el sistema.

---

## 3. Práctica virtual guiada — Diseño conceptual de un Data Center empresarial

**Escenario:** *"LogísticaAndina S.A."*, empresa de 250 empleados, quiere diseñar su Data
Center **principal** en la casa matriz. Requisitos:

- Debe alojar ~30 servidores (futuro Rack virtualizado en sesión 2).
- Servicios críticos: ERP, base de datos, correo, backup.
- SLA objetivo: **99,99 %** de disponibilidad.
- Presupuesto: finito (evitar 2N completo, pero **justificar**).

### Paso 1 — Estimar la carga de TI

| Recurso | Cantidad | Consumo unitario | Subtotal |
| --- | --- | --- | --- |
| Servidores 2U | 30 | 500 W | 15.000 W |
| Switches | 8 | 150 W | 1.200 W |
| Storage SAN | 4 | 700 W | 2.800 W |
| Firewalls | 2 | 200 W | 400 W |
| **Total TI** | | | **19.400 W (19,4 kW)** |

### Paso 2 — Definir redundancia (energía y clima)

- Decisión propuesta: **N+1** en UPS, generador y climatización (justificado por costo/SLA).
- Dos acometidas eléctricas independientes si el edificio lo permite.

### Paso 3 — Dimensionar UPS y generador

- Carga + 30 % de crecimiento → **19,4 kW × 1,3 ≈ 25,2 kW**.
- UPS: elegir **N+1** con autonomía de ≥ 10 min a carga plena (para arranque del generador).
- Generador: dimensionar a ≥ 30 kW con arranque automático vía **ATS** y combustible ≥ 24 h.

### Paso 4 — Climatización

- Carga térmica ≈ carga eléctrica de TI (casi toda se convierte en calor) → **~25 kW**.
- Solución: **2 unidades de CRAC/CRAH en N+1** con **contención de pasillo caliente**.
- Objetivo **PUE ≤ 1,4**.

### Paso 5 — Racks y networking

- 30 servidores 2U + switches + storage → **mínimo 4 racks de 42U** (dejar 30 % libre).
- Red **spine–leaf**, uplinks **25/100 GbE**, **VLANs** para gestión/producción/storage/backup.

### Paso 6 — Seguridad física y monitoreo

- Acceso con **tarjeta + biometría**, CCTV con retención 90 días, sensores ambientales,
  integración a **DCIM**.

### Entregable

Un **diagrama en draw.io** con: layout de sala, racks, rutas eléctricas, unidades de clima,
red spine-leaf y leyenda de redundancia. Acompañar con una tabla de justificaciones.

![Diagrama base de la práctica 1](practicas/sesion-01-dc-empresarial.png)

> **Plantilla base editable:** [`practicas/sesion-01-dc-empresarial.drawio`](practicas/sesion-01-dc-empresarial.drawio)
> · vectorial [`SVG`](practicas/sesion-01-dc-empresarial.svg)
> · abrir en draw.io / diagrams.net y completar.

### Plantilla de cierre

```
Data Center: ______________   Tier objetivo: ____
Carga TI: ____ kW   UPS: ______ (N+1)   Autonomía: ___ min
Generador: ___ kW   Combustible: ___ h   Clima: ______ (N+1)
Racks: ___   PUE estimado: ____   Seguridad: __________
Justificación de redundancia: __________________________
```

---

## 4. Ejercicios propuestos

**Ejercicio 1 — Nivel Tier.** Un DC tiene UPS y climatización **N+1**, mantenimiento
concurrente, pero una falla en un componente puede interrumpir la carga. ¿Qué Tier es?

**Ejercicio 2 — PUE.** Un DC consume 120 kW totales y la carga de TI es 80 kW. Calcula el
PUE e indica si es eficiente.

**Ejercicio 3 — UPS.** La carga es de 12 kW con factor de potencia 0,9. ¿Qué UPS en VA se
necesita como mínimo, agregando 25 % de margen?

**Ejercicio 4 — RAID.** Necesitas máxima tolerancia a fallos y buen rendimiento para la base
de datos. ¿RAID 5, 6 o 10? Justifica.

**Ejercicio 5 — Disponibilidad.** Un servicio cae 4 veces al año, 30 minutos cada vez.
¿Cuál es su disponibilidad aproximada y cuántos "nueves" tiene?

---

## 5. Solucionario

**Solución 1.** Es **Tier III**. La característica distintiva de Tier III es redundancia
**N+1** y **mantenimiento concurrente**, pero *no* tolera una falla sin interrumpir la carga.
El Tier IV requiere **2N/2N+1** y tolerancia a fallos.

**Solución 2.** `PUE = 120 / 80 = 1,5`. Es aceptable pero **mejorable**; el objetivo moderno
es acercarse a **1,3–1,4** o menos. Un PUE de 1,5 indica margen de optimización térmica.

**Solución 3.**
- Potencia real con FP: `P = VA × FP` → necesitamos `VA = W / FP = 12.000 / 0,9 ≈ 13.333 VA`.
- Con margen 25 %: `13.333 × 1,25 ≈ 16.667 VA`.
- **UPS mínimo ≈ 16,7 kVA** (en la práctica, elegir el modelo comercial ≥ ese valor, p. ej. 20 kVA).

**Solución 4.** **RAID 10.** Tolera fallos de hasta un disco por espejo con muy buena
escritura (ideal para bases de datos). **RAID 5** solo tolera 1 disco y castiga la escritura;
**RAID 6** tolera 2 pero penaliza más el rendimiento de escritura. RAIDS no sustituyen backups.

**Solución 5.**
- Caída total: `4 × 30 min = 120 min/año`.
- Minutos en un año: `365 × 24 × 60 = 525.600 min`.
- Disponibilidad `= 1 − (120 / 525.600) ≈ 0,999772` → **≈ 99,98 % ≈ 3,9 nueves** muy cerca de
  4 nueves (4 nueves = 52,6 min/año; aquí son 120 min → prácticamente **3,97 nueves**).

---

## 6. Recursos y lecturas sugeridas

- **Uptime Institute** — definiciones de Tier (documentación pública).
- **TIA-942** — estándar de infraestructura de Data Centers.
- **ASHRAE TC 9.9** — guías de entorno térmico.
- **Draw.io / diagrams.net** — para el entregable de diseño.
- Fabricantes para datasheets: APC/Schneider (UPS), Vertiv (clima), Dell/HPE (servidores).

---

## 7. Autoevaluación (quiz)

1. ¿Cuánto mide 1U en milímetros y cuál es la altura típica de un rack?
2. Diferencia entre RAID 5 y RAID 6.
3. ¿Qué significa **2N** y en qué se diferencia de **N+1**?
4. ¿Cuál es la diferencia esencial entre Tier III y Tier IV?
5. ¿Qué fórmula resume el PUE y para qué sirve?

<details>
<summary>Respuestas</summary>

1. 1U = 44,45 mm; rack típico = 42U.
2. RAID 5 tolera **1** fallo (1 paridad); RAID 6 tolera **2** (doble paridad).
3. **2N** = dos sistemas completos independientes; **N+1** = un sistema + un componente extra.
4. Tier IV tolera fallos **sin** interrumpir; Tier III tiene mantenimiento concurrente pero
   una falla puede interrumpir.
5. `PUE = Energía total / Energía de TI`; mide la eficiencia energética del DC.

</details>

---

## 8. Glosario de la sesión

| Término | Definición breve |
| --- | --- |
| **U / RU** | Unidad de altura de rack (1,75" / 44,45 mm). |
| **PDU** | Power Distribution Unit; distribuye energía en el rack. |
| **UPS** | Sistema de alimentación ininterrumpida. |
| **ATS** | Automatic Transfer Switch; conmuta entre acometidas/generador. |
| **CRAC/CRAH** | Unidades de climatización de sala. |
| **PUE** | Power Usage Effectiveness; eficiencia energética. |
| **BMC/IPMI/iDRAC/iLO** | Gestión fuera de banda del servidor. |
| **DAS/NAS/SAN** | Almacenamiento directo/en red por archivo/por bloques. |
| **ToR/EoR** | Top/End of Rack (ubicación de switches). |
| **Spine-Leaf** | Topología de red de DC moderna. |
| **DCIM** | Data Center Infrastructure Management. |
| **MTBF/MTTR** | Tiempo medio entre fallas / de reparación. |
