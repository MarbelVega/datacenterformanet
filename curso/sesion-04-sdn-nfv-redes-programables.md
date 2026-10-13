# Sesión 4 — SDN, NFV y Redes Programables

> **Duración:** 2 horas (20:00–22:00)
> **Modalidad:** Virtual
> **Práctica virtual:** Interpretación de una arquitectura SDN/NFV

---

## 1. Información general y objetivos

### Objetivos de aprendizaje

Al terminar esta sesión, el participante será capaz de:

1. Explicar **qué es SDN** y en qué se diferencia de las redes tradicionales.
2. Diferenciar los planos **de datos, de control y de gestión**.
3. Describir el rol de los **controladores SDN** e **interfaces** (norte/sur).
4. Explicar **NFV**, sus **VNF**, **NFVI** y el marco **ETSI MANO**.
5. Entender **APIs de red** y la base de la **automatización** (Ansible/Python/YANG).
6. Interpretar una **arquitectura SDN/NFV** real y detectar sus componentes.
7. Relacionar SDN/NFV con **redes programables** e **infraestructura autónoma**.

### Contenidos cubiertos

Software-Defined Networking · Control Plane · Data Plane · Controladores · APIs · NFV ·
Funciones virtualizadas · Network APIs · Automatización · Redes programables ·
Infraestructura autónoma.

---

## 2. Contenido teórico

### 2.1 ¿Qué es SDN?

**Software-Defined Networking** separa el **control** (decidir el camino) del **reenvío**
(mover los paquetes) y lo centraliza en software programable.

**Red tradicional:** cada dispositivo (router/switch) decide y reenvía por sí mismo; la
configuración es **manual, por dispositivo** y **propietaria**.

**Red SDN:** un **controlador central** tiene la "vista completa" y programa los dispositivos
mediante **interfaces estándar**. El comportamiento se define por **software/políticas**.

| Aspecto | Tradicional | SDN |
| --- | --- | --- |
| Control | Distribuido (por dispositivo) | **Centralizado** (controlador) |
| Configuración | Manual, CLI por equipo | **Programática / API** |
| Visibilidad | Limitada a cada equipo | **Global de la red** |
| Cambios | Lentos, por dispositivo | **Rápidos, automatizados** |

### 2.2 Planos de la red (Data / Control / Management)

```
┌────────────────────────────────────────────────┐
│ Management Plane  — administración/telemetría   │
├────────────────────────────────────────────────┤
│ Control Plane     — decide rutas y políticas    │  ← Controlador SDN
├────────────────────────────────────────────────┤
│ Data Plane (forwarding) — reenvía paquetes      │  ← switches físicos/virtuales
└────────────────────────────────────────────────┘
```

- **Data Plane (forwarding):** ejecuta el reenvío rápido (ASIC, hardware).
- **Control Plane:** calcula rutas, arma tablas, define políticas.
- **Management Plane:** configuración, monitoreo, telemetría.

### 2.3 Controladores SDN

El **controlador** es el cerebro: traduce políticas en reglas y las baja a los dispositivos.

Componentes comerciales / open source:

- **OpenDaylight**, **ONOS** (open source).
- **VMware NSX**, **Cisco Catalyst Center / ACI**, **Juniper Contrail**.

**Interfaces (direcciones de comunicación):**

| Interfaz | Dirección | Protocolos |
| --- | --- | --- |
| **Southbound (sur)** | Controlador → dispositivos | **OpenFlow**, NETCONF, gNMI, P4 |
| **Northbound (norte)** | Aplicaciones → controlador | **REST API** |
| **East/West** | Controlador ↔ controlador | Federación/clúster |

### 2.4 Overlay y Underlay

- **Underlay:** red física (spine-leaf, IP).
- **Overlay:** red lógica sobre el underlay (**VXLAN**, **GENEVE**, **EVPN**).
- Permite aislar y escalar tenants sin reconfigurar el hardware base.

### 2.5 NFV (Network Functions Virtualization)

**NFV** reemplaza **appliances de red dedicados** (firewall, router, balanceador) por
**software** corriendo en **máquinas virtuales o contenedores**.

**Ventajas:** menor CAPEX, agilidad, escalado, independencia del hardware.

**Marco ETSI MANO** (Management and Orchestration):

| Componente | Función |
| --- | --- |
| **VNF** | Virtual Network Function (función de red virtualizada). |
| **NFVI** | Infraestructura (cómputo, storage, red) donde corren las VNF. |
| **NFV-MANO** | Orquestación: VIM + VNFM + Orchestrator (NFVO). |
| **VIM** | Virtual Infrastructure Manager (p. ej. OpenStack, VMware). |

```
        ┌───────────────────────────────────┐
        │            NFV-MANO                │
        │  (Orquestador, VNFM, VIM)          │
        └───────────────┬───────────────────┘
                        │ despliega / gestiona
        ┌───────────────┴───────────────────┐
        │              NFVI                  │
        │  [VNF: FW] [VNF: Router] [VNF: LB] │
        └───────────────────────────────────┘
```

**SDN vs NFV (¡no son lo mismo!):**

- **SDN** → cómo se **controla y programa la red** (separar control de datos).
- **NFV** → cómo se **ejecutan las funciones de red** (virtualizarlas).
- **Se complementan**: NFV virtualiza funciones; SDN las interconecta y programa.

### 2.6 Network APIs y automatización

- Las plataformas modernas exponen **REST APIs / NETCONF / gNMI**.
- **Modelos de datos:** **YANG** describe la configuración; **OpenAPI/Swagger** documenta APIs.
- **Automatización:** **Ansible** (playbooks), **Python** (scripts/SDKs), **Terraform** (IaC).
- **Plantillas:** **Jinja2** para generar configuraciones en serie.
- **CI/CD para redes:** validar y desplegar cambios como código (NetDevOps).

```yaml
# Ejemplo de playbook Ansible (pseudo-real)
- hosts: switches
  gather_facts: false
  tasks:
    - name: Crear VLAN 30
      community.network.nos_config:
        lines:
          - vlan 30
          - name "Gestion"
```

### 2.7 Redes programables e infraestructura autónoma

- **Red programable:** el comportamiento se define por **software/API**, no por CLI manual.
- **Intent-Based Networking (IBN):** declarás el **intento** ("este tráfico debe ir por aquí
  con esta política") y el sistema lo traduce a configuración.
- **Infraestructura autónoma:** telemetría en tiempo real (**streaming telemetry**),
  **IA/ML** para detección de anomalías y **auto-remediación** (self-healing).

**Escalera de madurez:**

```
Manual → Automatizada (scripts) → Programable (APIs) → Basada en intención → Autónoma (IA)
```

---

## 3. Práctica virtual guiada — Interpretación de una arquitectura SDN/NFV

**Escenario:** se entrega el siguiente diagrama de una **red de sucursal empresarial** y hay
que **identificar** sus componentes, planos, interfaces y el rol de cada pieza.

```
┌─────────────────────── Oficina Central ────────────────────────┐
│                                                                │
│   ┌─── Aplicación de política (Portal/API) ───┐               │
│   │            Northbound (REST)               │               │
│   └────────────────────┬──────────────────────┘               │
│                        ▼                                       │
│           ┌──── Controlador SDN ────┐                          │
│           │  ONOS / NSX (clúster)   │                          │
│           └───────┬────────┬────────┘                          │
│       Southbound  │        │  Southbound                       │
│        (NETCONF)  │        │  (OpenFlow)                       │
│                   ▼        ▼                                   │
│            [Switch A]   [Switch B]  ← Underlay (física)        │
│                   │        │                                   │
│        ┌──────────┴────────┴──────────┐                        │
│        │   Overlay VXLAN (10.50.0.0/16)│                       │
│        │  [VNF FW] [VNF LB] [VNF VPN]  │  ← NFVI               │
│        └────────────────────────────────┘                      │
└────────────────────────────────────────────────────────────────┘
                          │
             Internet / Cloud (VPN)
```

### Tarea A — Identificar planos

| Elemento del diagrama | Plano / componente |
| --- | --- |
| Aplicación de política | **Management / aplicación northbound** |
| Controlador SDN | **Control Plane** |
| Switch A y Switch B | **Data Plane** |
| VNF FW / LB / VPN | **NFVI (funciones virtualizadas)** |

### Tarea B — Identificar interfaces

- Portal → Controlador = **Northbound (REST)**.
- Controlador → Switches = **Southbound (OpenFlow/NETCONF)**.

### Tarea C — Identificar la separación SDN vs NFV

- **SDN:** separación control/datos y programación de switches A/B desde el controlador.
- **NFV:** FW, LB y VPN existen como **software** corriendo en el NFVI, no como appliances.

### Tarea D — Red programable

- Un cambio de política se hace en **el controlador/API**, no entrando switch por switch.
- Con **telemetría streaming** y **auto-remediación**, la red avanza hacia **autónoma**.

### Entregable

Documento/anotación del diagrama con: (1) clasificación de planos, (2) interfaces y protocolos,
(3) separación de componentes SDN vs NFV, (4) qué elementos hacen la red "programable", y
(5) una propuesta de automatización (Ansible/Python) para aplicar un cambio repetible.

![Diagrama base de la práctica 4](practicas/sesion-04-arquitectura-sdn-nfv.png)

> **Plantilla base editable:** [`practicas/sesion-04-arquitectura-sdn-nfv.drawio`](practicas/sesion-04-arquitectura-sdn-nfv.drawio)
> · vectorial [`SVG`](practicas/sesion-04-arquitectura-sdn-nfv.svg)
> · abrir en draw.io / diagrams.net; usarla para anotar la interpretación.

---

## 4. Ejercicios propuestos

Los ejercicios están **desglosados en sub-ítems**: resuelva cada uno paso a paso y
justifique siempre la decisión técnica.

**Ejercicio 1 — Clasificación de planos (punto por punto).**
Para cada actividad, indique el plano al que pertenece (**Data, Control o Management**) y
**por qué**:
(a) calcular la tabla de ruteo;
(b) reenviar un paquete por la salida correcta;
(c) recolectar métricas de CPU y temperatura de un switch;
(d) instalar una actualización de firmware;
(e) decidir el camino de un flujo por una política de QoS;
(f) exponer la tabla de flujos a un sistema de monitoreo.

**Ejercicio 2 — Interfaces: esquema completo.**
(a) ¿Qué interfaz usa una aplicación para pedir una política al controlador? Dé un
protocolo/API de ejemplo.
(b) ¿Qué interfaz usa el controlador para programar un switch físico? Dé **2** protocolos.
(c) ¿Para qué sirven las interfaces **este/oeste** y en qué escenario aparecen?

**Ejercicio 3 — SDN, NFV o ambos: caso de análisis.**
Un router virtual (**VNF**) corre en una VM sobre Proxmox y su configuración se aplica desde
un **controlador central** vía API.
(a) ¿Qué parte del caso es **NFV** y qué parte es **SDN**?
(b) Si el controlador no existiera y el router se configurara por CLI en la VM, ¿qué parte
seguiría siendo NFV y cuál desaparecería?
(c) ¿Puede existir **NFV sin SDN**? ¿Y **SDN sin NFV**? Justifique con un ejemplo de cada.

**Ejercicio 4 — Overlay y Underlay.**
(a) Defina **overlay** y **underlay** en el contexto SDN.
(b) Cite **2 protocolos de overlay** y **1 tecnología del underlay**.
(c) ¿Qué problema resuelve **VXLAN** frente a las VLAN tradicionales (límite 4094)?

**Ejercicio 5 — Automatización: herramientas y un caso concreto.**
(a) Mencione **3 herramientas** de automatización de red y qué aporta cada una.
(b) Escriba (pseudocódigo o YAML real de Ansible) un playbook que aplique a **10 switches**:
crear la **VLAN 30**, nombrarla `Gestion` y configurar la interfaz de gestión.
(c) ¿Qué rol cumplen los **modelos YANG** en esa automatización?

**Ejercicio 6 — Escalera de madurez.**
(a) Ordene de menor a mayor: Autónoma, Manual, Basada en intención, Programable,
Automatizada.
(b) ¿Cuál es el escalón **mínimo** para que una red se considere *programable*? Diferencie
de la **automatizada**.

**Ejercicio 7 — Mini interpretación (aplica la práctica).**
Dado: `Aplicación de política → Controlador (ONOS) → 3 switches → VNF (firewall + LB)`:
(a) Enumere los planos presentes.
(b) Marque en un esquema las interfaces **norte** y **sur**.
(c) Indique qué elementos pertenecen al **NFVI**.

**Ejercicio 8 — Resolución de un problema real.**
Un administrador configura 40 switches **por CLI** y tarda ~4 horas en aplicar un cambio de
VLAN. Proponga una solución de **redes programables** en 3 pasos concretos y cuantifique la
mejora esperada.

---

## 5. Solucionario

**Solución 1 (desglose plano a plano):**
(a) **Control Plane** — *decide* las rutas (routing/OSPF/BGP); es una decisión, no reenvío.
(b) **Data Plane (forwarding)** — el reenvío *ejecuta* la decisión paquete a paquete.
(c) **Management Plane** — telemetría y administración; no participa del tráfico.
(d) **Management Plane** — tarea administrativa sobre el dispositivo (firmware).
(e) **Control Plane** — la política de QoS se traduce en criterios de reenvío *decididos*
desde el control, aunque el hardware (Data) los ejecute.
(f) **Data Plane + Management Plane** — la tabla de flujos *vive* en el Data Plane, pero
*exponerla* a monitoreo es función de gestión/telemetría.

**Solución 2.**
(a) Interfaz **Northbound** — ejemplo: **REST API** (la aplicación consume el controlador).
(b) Interfaz **Southbound** — ejemplos: **OpenFlow**, **NETCONF**, **gNMI** (cualesquiera 2).
(c) Las interfaces **este/oeste** sincronizan **controladores en clúster** (alta
disponibilidad y federación): si un controlador cae, el par asume sin perder el estado
de la red.

**Solución 3.**
(a) **NFV:** el router virtual (VNF) corriendo como software en la VM. **SDN:** que un
controlador central programe su configuración vía API en lugar de CLI por dispositivo.
(b) Seguiría siendo **NFV** (la función sigue virtualizada); **desaparecería el componente
SDN** (ya no hay control programable desde un controlador).
(c) **Sí, NFV sin SDN:** un firewall virtual configurado manualmente (sin controlador).
**Sí, SDN sin NFV:** un controlador programando switches físicos (VXLAN/OpenFlow) sin
funciones virtualizadas.

**Solución 4.**
(a) **Underlay:** red física que transporta el tráfico (IP + switches). **Overlay:** red
lógica construida sobre el underlay que aísla tenants sin tocar el hardware.
(b) Overlay: **VXLAN**, **GENEVE** (también válido EVPN). Underlay: **spine-leaf IP** o
fabric física.
(c) VXLAN usa identificadores de 24 bits (**≥ 16 millones** de redes lógicas frente al
límite de **4094** VLAN), permitiendo multi-tenant sobre la misma infraestructura física.

**Solución 5.**
(a)
- **Ansible:** automatización declarativa por *playbooks*, idempotente, ideal para
  configurar muchos equipos en paralelo.
- **Python (Netmiko/Nornir/SDK):** lógica a medida, orquestación compleja y parsing.
- **Terraform:** infraestructura como código (IaC), plan/estado y cambios reproducibles.
(b) Ejemplo de playbook (solo pseudocódigo/YAML, no se ejecuta en el curso):
```yaml
- hosts: switches        # 10 switches
  gather_facts: false
  tasks:
    - name: Crear VLAN 30 "Gestion"
      community.network.nos_config:
        lines:
          - vlan 30
          - name Gestion
    - name: Configurar interfaz de gestión
      community.network.nos_config:
        lines:
          - interface vlan 30
          - ip address 10.0.30.1/24
```
(c) **YANG** modela la configuración (contenido) y **NETCONF/gNMI** transporta ese modelo
de forma estándar; sin YANG cada proveedor tendría una API propietaria e incompatible.

**Solución 6.**
(a) Manual → **Automatizada** → **Programable** → **Basada en intención** → **Autónoma**.
(b) El escalón mínimo es **Programable**: la red (o su controlador) expone **APIs** que
permiten cambiar comportamiento por software. La diferencia con **automatizada**: la
automatizada usa scripts que repiten pasos, pero si no expone APIs, cada cambio sigue
dependiendo de la CLI y de la herramienta; en la programable el dispositivo es *ciudadano*
de un ecosistema API.

**Solución 7.**
(a) **Management** (aplicación de política), **Control** (ONOS), **Data** (switches) y
**NFVI** (VNF).
(b) Norte: `Aplicación → ONOS` (REST). Sur: `ONOS → switches` (OpenFlow/NETCONF).
(c) NFVI: el host y la infraestructura donde corren las **VNF firewall + LB**.

**Solución 8.**
1. **Centralizar el control:** desplegar un controlador (p. ej., ONOS/OpenDaylight) o
plataforma con API sobre los 40 switches.
2. **Modelar la configuración** (playbooks Ansible + YANG/NETCONF o API REST) para
definir "VLAN nueva → aplicar a switches X" como código.
3. **Versionar y automatizar el despliegue** (Git + pipeline): el cambio se valida
(tests) y se aplica en línea de comandos de una sola vez.
Mejora esperada: de ~4 h manuales a **minutos** (a menudo < 15 min) con consistencia
garantizada y trazabilidad (quién, qué, cuándo).

---

## 6. Recursos y lecturas sugeridas

- **ETSI** — marco NFV y MANO (documentos públicos).
- **Open Networking Foundation (ONF)** — OpenFlow y SDN.
- **ONOS / OpenDaylight** — controladores open source.
- **Ansible Network Automation** y **Cisco DevNet** — laboratorios.
- **RFC 8309** (YANG) y guías de **NETCONF/gNMI**.

---

## 7. Autoevaluación (quiz)

1. ¿Qué separa SDN entre plano de control y de datos?
2. Enumera las interfaces norte y sur y un protocolo de cada una.
3. ¿Qué significa **VNF** y dónde corre?
4. ¿SDN y NFV son lo mismo? Justifica.
5. ¿Qué es la **red basada en intención** (IBN)?

<details>
<summary>Respuestas</summary>

1. SDN centraliza el **control** en software y deja el **reenvío** en el hardware.
2. Norte = **REST**; Sur = **OpenFlow/NETCONF/gNMI**.
3. **Virtual Network Function**: función de red en software; corre en el **NFVI** (VMs/contenedores).
4. No. SDN controla/programa la red; NFV virtualiza las funciones de red. Se complementan.
5. Declarar el **objetivo de negocio** ("intent") y que el sistema lo traduzca a configuración.

</details>

---

## 8. Glosario de la sesión

| Término | Definición breve |
| --- | --- |
| **SDN** | Redes definidas por software (control centralizado y programable). |
| **Data Plane** | Reenvío de paquetes. |
| **Control Plane** | Decisión de rutas y políticas. |
| **Management Plane** | Administración y telemetría. |
| **OpenFlow** | Protocolo southbound clásico de SDN. |
| **Controlador SDN** | Cerebro central que programa la red. |
| **NFV** | Virtualización de funciones de red. |
| **VNF / NFVI** | Función virtualizada / infraestructura que la aloja. |
| **ETSI MANO** | Marco de gestión y orquestación NFV. |
| **YANG / NETCONF / gNMI** | Modelado y provisión de configuración. |
| **IBN** | Intent-Based Networking (redes por intención). |
