# Sesión 5 — SD-WAN, SASE y Conectividad Segura para Empresas Multisede

> **Duración:** 2 horas (20:00–22:00)
> **Modalidad:** Virtual
> **Práctica virtual:** Diseño de una infraestructura multisede moderna

---

## 1. Información general y objetivos

### Objetivos de aprendizaje

Al terminar esta sesión, el participante será capaz de:

1. Explicar las limitaciones de la **WAN tradicional** (MPLS) y por qué surge SD-WAN.
2. Describir la arquitectura **SD-WAN**, sus controladores y la **selección dinámica de enlaces**.
3. Definir **políticas** de tráfico y **optimización** de WAN.
4. Explicar **SASE** y **SSE**, y sus componentes (SWG, CASB, ZTNA, FWaaS, DLP).
5. Diferenciar **ZTNA** de la **VPN** tradicional.
6. Explicar **microsegmentación** y **arquitectura multisede**.
7. Diseñar una **infraestructura multisede moderna** justificando decisiones.

### Contenidos cubiertos

WAN tradicional · SD-WAN · Multi-WAN · Selección dinámica de enlaces · Políticas ·
Optimización · Cloud Connectivity · SASE · SSE · ZTNA · Microsegmentación ·
Arquitectura multisede.

---

## 2. Contenido teórico

### 2.1 WAN tradicional

La **WAN** conecta sedes geográficamente separadas. El modelo clásico se apoya en:

- **MPLS** (proveedor, garantías de SLA, caro y rígido).
- Enlaces dedicados y **VPN IPsec** sobre Internet para respaldo.
- **Backhaul** de todo el tráfico hacia el **Data Center central** (modelo hub-and-spoke).

**Limitaciones:**

- Costos altos y escalado lento (contratos, plazos).
- Tráfico de nube y SaaS obligado a "volver" al DC (**tromboning**) → latencia.
- Configuración manual, poca visibilidad y pocas herramientas de política.

### 2.2 SD-WAN

**SD-WAN** aplica los principios de SDN a la WAN: **overlay** sobre cualquier transporte
(MPLS, Internet, LTE/5G, satélite) con **control centralizado** y **políticas por aplicación**.

**Arquitectura típica:**

```
┌──── Control / Orquestación (cloud) ────┐
│  Controlador + Orquestador + Analytics  │  ← gestiona políticas y telemetría
└───────────────┬───────────────┬─────────┘
                │               │
        ┌───────┴───┐     ┌─────┴─────┐
        │  Edge Sede A │   │ Edge Sede B │   ← cEdge/vEdge (CPE virtual o HW)
        └──┬────┬────┬─┘   └──┬────┬────┬─┘
         MPLS   Int  5G     MPLS   Int  5G   ← múltiples transportes (Multi-WAN)
```

**Conceptos clave:**

- **Overlay:** túneles (IPsec) sobre los distintos transportes.
- **Edge (CPE):** appliance físico o **virtual (vCPE)** en cada sede.
- **Controlador central:** gestiona políticas, claves y enrutamiento.
- **Zero-Touch Provisioning (ZTP):** el equipo se auto-configura al conectar.
- **Application-Aware Routing:** decide el mejor camino **por aplicación**.

### 2.3 Multi-WAN y selección dinámica de enlaces

- La sede tiene **varios enlaces** (MPLS + fibra + 5G).
- **Sondas (probes)** miden latencia, jitter, pérdida y **cumplimiento de SLA** por enlace.
- **Selección dinámica:** el tráfico crítico (voz, video) va por el enlace que cumple SLA;
  el tráfico de mejor esfuerzo usa el más barato.
- **Failover / failback** automático y **balanceo** de carga.

### 2.4 Políticas y optimización

**Políticas (ejemplos):**

| Aplicación | Prioridad | Enlace preferido | Acción |
| --- | --- | --- | --- |
| VoIP / Teams | Alta | Enlace con menor latencia | Failover inmediato |
| ERP | Alta | MPLS | Prioridad |
| Backup | Baja | Internet (bajo costo) | Limitado por horario |
| YouTube/Catálogo | Baja | Internet | Best-effort |

**Optimización de WAN:**

- **FEC** (corrección de errores), **deduplicación**, **compresión**.
- **TCP optimization** y **caching**.
- **Seguridad integrada:** IPsec, firewall, segmentación por VPN del overlay.

### 2.5 Cloud Connectivity

- **Cloud on-ramps**: conexiones optimizadas a AWS/Azure/GCP (p. ej. **AWS Transit
  Gateway**, **Azure Virtual WAN**).
- **Local breakouts** para SaaS: el tráfico va directo a Internet/cloud en vez de al DC.
- **vCPE / edge en el cloud** para ramas ligeras.

### 2.6 SASE y SSE

**SASE = SD-WAN + SSE** (Secure Access Service Edge): fusiona **conectividad** y
**seguridad** en un servicio **cloud-native**, entregado cerca del usuario (edge).

**SSE (Security Service Edge)** = las funciones de seguridad de SASE:

| Componente | Descripción |
| --- | --- |
| **SWG** | Secure Web Gateway: filtrado de web/URL. |
| **CASB** | Cloud Access Security Broker: control de SaaS. |
| **ZTNA** | Zero Trust Network Access: acceso por identidad al recurso. |
| **FWaaS** | Firewall as a Service. |
| **DLP** | Data Loss Prevention: prevención de fuga de datos. |

```
        SASE = SD-WAN (conexión) + SSE (seguridad)
        ┌─────────────────────────────────────────┐
        │  Conectividad        +      Seguridad     │
        │  (overlay, SLA)   (SWG/CASB/ZTNA/FWaaS)   │
        └─────────────────────────────────────────┘
                   entregado desde la nube (edge)
```

### 2.7 ZTNA vs VPN

| Aspecto | VPN tradicional | **ZTNA** |
| --- | --- | --- |
| Modelo | "Entrar a la red" (perímetro) | "Acceder al recurso" (identidad) |
| Acceso | Amplio (toda la red) | **Por aplicación/recurso** |
| Confianza | Confía una vez autenticado | **Zero Trust** (verifica siempre) |
| Ataque lateral | Riesgo de movimiento lateral | **Microsegmentado** |

### 2.8 Microsegmentación

- Divide la red en **segmentos finos** según **identidad, rol y aplicación**, no solo por IP.
- Reduce el **movimiento lateral** en caso de compromiso.
- Se implementa con políticas east-west (entre workloads), en DC y en cloud.

### 2.9 Arquitectura multisede

Diseñar para **varias sedes** implica combinar todo lo aprendido:

- **Transportes diversos** por sede (MPLS/Internet/5G) con SD-WAN.
- **Políticas centralizadas** y **local breakout** para navegación/SaaS.
- **Seguridad unificada** con **SASE/SSE** y **ZTNA** (sin exponer la red interna).
- **Conectividad a cloud** (on-ramps) y al Data Center híbrido (sesiones 1–3).
- **Alta disponibilidad** extremo a extremo y **gestión centralizada**.

### 2.10 Stack gratuito / capas gratuitas para laborar

Los productos empresariales (Cisco SD-WAN, VeloCloud, Fortinet, etc.) son de pago, pero
**cada concepto se puede practicar con capas gratuitas o software open source**:

| Función del diseño | Alternativa gratuita / free tier para el laboratorio |
| --- | --- |
| Overlay / malla de sedes | **Tailscale** (free) o **ZeroTier** (free): malla + *subnet routers* |
| Túneles site-to-site | **WireGuard** (open source) |
| SD-WAN: multi-WAN, failover, políticas | **OPNsense** o **pfSense CE** — *gateway groups* + reglas |
| ZTNA (acceso por recurso) | **Cloudflare Zero Trust** (free ≤ 50 usuarios) / ACLs de Tailscale |
| SWG / filtrado DNS | **Cloudflare Gateway** (free), **NextDNS**, **Pi-hole** (self-hosted) |
| Microsegmentación | ACLs de Tailscale, o VLAN + reglas en OPNsense, o Cilium (Kubernetes) |
| Cloud connectivity | Free tiers: **GCP e2-micro (Always Free)**, **AWS Free Tier**, **Oracle Always Free** |
| CI/CD y automatización | **Ansible** (open source), **GitHub Actions** (free) |
| Diagramas | **draw.io** (gratis) |

> Regla de oro del laboratorio: **no hace falta gastar** para entender la arquitectura
> multisede. Se emula el *comportamiento* de SD-WAN/SASE con estas piezas y se documenta
> qué aportaría cada componente comercial en producción.

---

## 3. Práctica virtual guiada — Multisede moderna con capas gratuitas (free tier)

**Escenario:** *"LogísticaAndina S.A."* (de sesión 1) tiene ahora **3 sedes**:

- **Central** (casa matriz, con Data Center principal).
- **Sucursal Norte** (mediana, 60 usuarios).
- **Sucursal Sur** (pequeña, 20 usuarios, con 5G de respaldo).

Requisito del laboratorio: diseñar la solución multisede usando **SOLO capas gratuitas**
(free tiers y open source del bloque 2.10). Los componentes comerciales se mencionan solo
como referencia de qué aportan en producción.

### Paso 1 — Malla overlay gratuita entre sedes

- Crear una cuenta (**free**) de **Tailscale** o **ZeroTier** y armar una red privada
  virtual con las 3 sedes + 1 nodo cloud.
- En la Central, configurar un **subnet router** para publicar la LAN del ERP/DB **sin
  abrir puertos** ni usar VPN tradicional.
- Comprobar conectividad extremo a extremo (`ping`, `tailscale status` /
  `zerotier-cli`).

### Paso 2 — Simular SD-WAN con OPNsense/pfSense (multi-WAN + failover)

- Instalar **OPNsense** (free) en cada sede con **2 WANs**: fibra + 4G/5G (hotspot/USB).
- Crear **gateway groups** (failover + *load balancing*) y reglas por puerto/aplicación.
- Monitorear latencia con el panel de gateways y probar el **failover** cortando el
  enlace principal (equivale a la *probe SLA* del SD-WAN comercial).

### Paso 3 — Políticas por aplicación

| Tráfico | Implementación gratuita |
| --- | --- |
| ERP (a la Central) | Regla fija → gateway principal; failover al 4G/5G |
| Microsoft 365 / navegación | Salida local (*local breakout* / default route) |
| VoIP/Teams | Prioridad por regla; si sube el jitter, el gateway group lo mueve al enlace secundario |
| Backup a cloud | De noche, con límite de ancho de banda por regla (OPNsense) |

### Paso 4 — SASE/SSE con free tiers

- **ZTNA:** **Cloudflare Zero Trust** (free ≤ 50 usuarios): *Access* (autenticación por
  identidad) + *Tunnel* para publicar el ERP **sin exponer IP pública**.
- **SWG:** **Cloudflare Gateway** o **NextDNS** para filtrar web/DNS malicioso.
- **Microsegmentación:** grupos + **ACLs de Tailscale** (o VLAN + reglas OPNsense): solo
  lo necesario puede comunicarse.

### Paso 5 — Conectividad a cloud (free tier)

- Alta una instancia **e2-micro (GCP Always Free)** o **t2.micro (AWS Free Tier)**.
- Conéctela a la malla (Tailscale/WireGuard) simulando el *cloud on-ramp*.
- Configure un bucket de Storage free tier para el **backup nocturno** desde OPNsense.

### Diagrama esperado (esquema)

```
                   ┌─ SASE/SSE free (cloud) ────────────┐
                   │  Cloudflare Access+Tunnel · Gateway│
                   │  NextDNS · Tailscale ACLs          │
                   └────┬───────────────┬───────────────┘
                        │               │
    ┌──── Central ──────┴───┐   ┌── Norte ──┐   ┌── Sur ──┐
    │ DC + OPNsense (2 WAN) │   │ OPNsense  │   │ OPNsense│
    │ Tailscale subnet router│  │ 2 WAN     │   │ 2 WAN   │
    └──────────┬─────────────┘   └────┬─────┘   └────┬────┘
               │   Tailscale / WireGuard overlay      │
               └──────────────┬───────────────────────┘
                    Local breakout → M365 / navegación
                    Cloud on-ramp → e2-micro + bucket free tier
```

### Entregable

Diagrama draw.io de la infraestructura multisede **con capas gratuitas** + tabla
concepto → herramienta free tier + políticas implementadas (reglas/failover) + evidencia
(2-3 capturas de Tailscale/OPNsense/Cloudflare).

![Diagrama base de la práctica 5](practicas/sesion-05-infra-multisede.png)

> **Plantilla base editable:** [`practicas/sesion-05-infra-multisede.drawio`](practicas/sesion-05-infra-multisede.drawio)
> · vectorial [`SVG`](practicas/sesion-05-infra-multisede.svg)
> · abrir en draw.io / diagrams.net y completar.

---

## 4. Ejercicios propuestos

**Ejercicio 1 — Mapeo free tier.** Complete la tabla asociando cada concepto con su
herramienta gratuita: (a) overlay de sedes; (b) ZTNA; (c) SWG/filtrado DNS;
(d) multi-WAN/failover; (e) cloud on-ramp.

**Ejercicio 2 — Local breakout.** ¿Qué problema de la WAN tradicional resuelve el
**local breakout** para SaaS y cómo se replica con capas gratuitas?

**Ejercicio 3 — Failover en OPNsense.** ¿Con qué componente de OPNsense/pfSense se emula la
"selección dinámica de enlaces" del SD-WAN? Explique cómo probar el failover en el lab.

**Ejercicio 4 — VoIP.** El tráfico de **Teams** tiene jitter alto en un enlace. Proponga una
regla concreta (con una herramienta gratuita del lab) para priorizar el enlace bueno y qué
pasa automáticamente si ese enlace se cae.

**Ejercicio 5 — ZTNA vs VPN.** Con **Cloudflare Access/Tunnel** o **ACLs de Tailscale**,
¿cómo asegura que un usuario remoto acceda **solo al ERP** y no vea el resto de la red (a
diferencia de la VPN tradicional)?

**Ejercicio 6 — Microsegmentación.** Para una impresora de red y la base de datos, ¿conviene
la misma política de microsegmentación? Escriba **1 regla/ACL** (Tailscale u OPNsense) para
la impresora.

**Ejercicio 7 — Límites del enfoque gratuito.** Mencione **3 capacidades** de SD-WAN/SASE
comerciales (p. ej., Cisco o Fortinet) que el lab gratuito **NO** reproduce y qué riesgo
implicaría ignorarlas en producción.

---

## 5. Solucionario

**Solución 1.**
(a) overlay de sedes → **Tailscale / ZeroTier**; (b) ZTNA → **Cloudflare Access+Tunnel** o
ACLs de Tailscale; (c) SWG/filtrado DNS → **Cloudflare Gateway / NextDNS**;
(d) multi-WAN/failover → **OPNsense/pfSense (gateway groups)**; (e) cloud on-ramp →
**e2-micro (GCP) / t2.micro (AWS)** + WireGuard/Tailscale.

**Solución 2.** Evita el **tromboning**: en vez de mandar el tráfico SaaS al Data Center
central y de ahí a Internet (latencia y costo), el **breakout local** lo envía directo a
Internet/cloud desde la propia sede. En el lab se replica con la **salida local por defecto**
en OPNsense: cada sede navega/usar M365 sin pasar por la Central.

**Solución 3.** Los **gateway groups** de OPNsense/pfSense con monitoreo de gateway. Se
combina *failover* + *load balancing* y se prueba **cortando el enlace principal**: el
tráfico migra automáticamente al gateway secundario (equivale a la *probe SLA* que mueve
flujos según latencia/jitter/pérdida en SD-WAN).

**Solución 4.** Crear en OPNsense una **regla fija** para el puerto/red de Teams anclada al
**gateway group con failover**: normalmente sale por el enlace de menor latencia; si el
gateway primario pierde el monitoreo (jitter/latencia), el grupo conmuta al secundario sin
intervención manual.

**Solución 5.** Con **ZTNA** el acceso se da **por recurso/identidad**: Cloudflare
**Access** autentica y el **Tunnel** publica solo el ERP (sin IP pública); las **ACLs de
Tailscale** limitan el tráfico a la `IP:puerto` del ERP. La **VPN tradicional**, al
autenticar, entrega acceso a **toda la red**, facilitando el **movimiento lateral** si la
credencial se ve comprometida.

**Solución 6.** **No.** Cada activo tiene distinta criticidad y rol de identidad. La
impresora (riesgo bajo) y la base de datos (crítica) deben estar en **segmentos separados**;
la microsegmentación se basa en identidad/rol. Ejemplo de ACL Tailscale:
`impresora → segmento oficina: 100.64.0.0/24` y **deny a todo lo demás** (incluida la DB).

**Solución 7.** Ejemplos válidos (bastan 3): (1) **telemetría/analítica avanzada** con vista
unificada del tráfico y SLAs end-to-end; (2) **soporte, acuerdos de nivel y garantía** del
proveedor (el open source no ofrece SLA empresarial); (3) **integración nativa de seguridad**
(CASB/DLP profundo, FWaaS global) y **aceleración WAN** en hardware para enlaces lentos.
Riesgo de ignorarlas: sin telemetría/SLA no se puede *probar* calidad end-to-end; sin
CASB/DLP quedan huecos de cumplimiento; y la aceleración WAN importa en circuitos
caros/lentos.

---

## 6. Recursos y lecturas sugeridas

- **MEF** — estándares de SD-WAN (MEF 70) y SASE.
- **Gartner** — definición y guías de SASE/SSE.
- **AWS Transit Gateway** y **Azure Virtual WAN** — conectividad cloud.
- Documentación de proveedores: Cisco SD-WAN, VMware VeloCloud, Fortinet, Palo Alto.
- **NIST SP 800-207** — Zero Trust Architecture.

---

## 7. Autoevaluación (quiz final integrador)

1. ¿Qué limitación de MPLS motiva la adopción de SD-WAN?
2. ¿Qué mide un **probe** de SD-WAN y para qué sirve?
3. Descompón **SASE** en sus dos mitades.
4. Diferencia central entre **ZTNA** y **VPN**.
5. ¿Qué es **microsegmentación** y qué riesgo mitiga?

<details>
<summary>Respuestas</summary>

1. Costo alto, rigidez, plazos largos y **tromboning** del tráfico hacia el DC.
2. Latencia, jitter, pérdida y **cumplimiento de SLA** por enlace; se usa para **selección
   dinámica** y failover.
3. **SD-WAN** (conectividad) + **SSE** (seguridad).
4. ZTNA da acceso **por recurso/aplicación** por identidad (zero trust); la VPN da acceso
   **amplio a la red**.
5. Dividir la red en segmentos finos por identidad/rol; mitiga el **movimiento lateral**.

</details>

---

## 8. Glosario de la sesión

| Término | Definición breve |
| --- | --- |
| **MPLS** | Transporte WAN dedicado con SLA. |
| **SD-WAN** | WAN definida por software, con overlay y políticas. |
| **Overlay / Underlay** | Red lógica sobre la red física. |
| **ZTP** | Zero-Touch Provisioning (auto-configuración). |
| **SLA probe** | Sonda que mide calidad de un enlace. |
| **Local breakout** | Salida directa a Internet/cloud desde la sede. |
| **SASE** | Secure Access Service Edge (red + seguridad). |
| **SSE** | Security Service Edge (solo seguridad). |
| **SWG / CASB / DLP** | Gateway web / broker SaaS / prevención de fuga. |
| **ZTNA** | Acceso a recursos por identidad (Zero Trust). |
| **Microsegmentación** | Segmentación fina por identidad/rol. |

---

## Cierre del curso

Esta sesión cierra el recorrido completo: del **piso físico** del Data Center (S1) a la
**virtualización** (S2), la **extensión a la nube** (S3), la **programabilidad** (S4) y la
**conectividad segura multisede** (S5). El participante ya puede encadenar las cinco prácticas
en un **dossier de arquitectura** coherente para un caso empresarial real.
