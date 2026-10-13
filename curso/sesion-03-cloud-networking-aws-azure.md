# Sesión 3 — Cloud Networking: AWS, Azure y Arquitecturas Híbridas

> **Duración:** 2 horas (20:00–22:00)
> **Modalidad:** Virtual
> **Práctica virtual:** Diseño de una arquitectura híbrida Cloud + On-Premise

---

## 1. Información general y objetivos

### Objetivos de aprendizaje

Al terminar esta sesión, el participante será capaz de:

1. Diferenciar los modelos de servicio **IaaS, PaaS y SaaS**.
2. Diferenciar los modelos de despliegue **público, privado e híbrido**.
3. Diseñar una **VPC (AWS)** y una **VNet (Azure)** con **subredes** públicas y privadas.
4. Explicar el rol de **gateways, route tables, NAT, security groups y NACL/NSG**.
5. Elegir el **balanceador de carga** adecuado al escenario.
6. Diseñar **conectividad híbrida** (VPN site-to-site, Direct Connect / ExpressRoute).
7. Aplicar principios de **seguridad cloud** y **responsabilidad compartida**.

### Contenidos cubiertos

IaaS · PaaS · SaaS · Cloud público/privado/híbrido · AWS · Microsoft Azure · VPC/VNet ·
Subnets · Gateways · Load Balancers · VPN Cloud · On-Premise + Cloud · Multi-Cloud ·
Seguridad Cloud.

---

## 2. Contenido teórico

### 2.1 Modelos de servicio

| Modelo | Qué provee el proveedor | Qué gestiona el cliente | Ejemplo |
| --- | --- | --- | --- |
| **IaaS** | Cómputo, red, storage | SO, runtime, apps | EC2, Azure VM |
| **PaaS** | Runtime + plataforma | Solo la app y sus datos | App Service, RDS |
| **SaaS** | Aplicación completa | Solo el uso y los datos | Microsoft 365, Gmail |

Analogía: **IaaS** = alquilar el terreno y los cimientos; **PaaS** = alquilar la casa con
servicios; **SaaS** = alquilar el departamento amoblado.

### 2.2 Modelos de despliegue

- **Cloud público:** recursos del proveedor, multi-tenant. Escalable, pago por uso.
- **Cloud privado:** infraestructura dedicada a una organización.
- **Cloud híbrido:** mezcla on-premise + cloud con conectividad y portabilidad.
- **Multi-cloud:** uso simultáneo de **varios proveedores** (AWS + Azure + GCP).

### 2.3 AWS — Redes (VPC)

Una **VPC (Virtual Private Cloud)** es una red virtual aislada dentro de una **Región**,
subdividida en **Availability Zones (AZ)**.

```
VPC 10.0.0.0/16
├── Subnet pública  10.0.1.0/24  (AZ-a)  ← IGW   (recursos alcanzables desde Internet)
├── Subnet privada  10.0.11.0/24 (AZ-a)  ← NAT GW (salida sin entrada)
├── Subnet pública  10.0.2.0/24  (AZ-b)
└── Subnet privada  10.0.12.0/24 (AZ-b)
```

**Componentes clave:**

| Componente | Función |
| --- | --- |
| **IGW** (Internet Gateway) | Entrada/salida a Internet para subredes públicas. |
| **NAT Gateway** | Salida a Internet para subredes privadas (sin entrada). |
| **Route Table** | Reglas de enrutamiento por subred. |
| **Security Group** | Firewall *stateful* a nivel de instancia. |
| **NACL** | Firewall *stateless* a nivel de subred. |
| **VPC Peering** | Conexión entre VPCs. |
| **Transit Gateway** | Hub central para muchas VPCs y on-premise. |

**Balanceadores:**

| Tipo | Uso |
| --- | --- |
| **ALB** (Application LB) | Capa 7, HTTP/HTTPS, routing por path/host. |
| **NLB** (Network LB) | Capa 4, altísimo rendimiento, TCP/UDP. |
| **GWLB** | Capa 3, appliances de red. |

### 2.4 Azure — Redes (VNet)

Equivalencias conceptuales directas:

| AWS | Azure |
| --- | --- |
| VPC | **VNet** |
| Subnet | **Subnet** |
| Security Group | **NSG** (Network Security Group) |
| IGW / NAT GW | **Internet/NAT** |
| Transit Gateway | **Virtual WAN** |
| Direct Connect | **ExpressRoute** |
| Route Table | **Route Table / UDR** |

- **Azure LB:** capa 4 (red) y **Application Gateway** capa 7 (con **WAF**).
- **VNet Peering:** conexión entre VNets (regional y global).
- **Resource groups** y **subscriptions** para organización y control de costos.

### 2.5 Conectividad híbrida (On-Premise + Cloud)

| Opción | Descripción | Latencia/Ancho |
| --- | --- | --- |
| **VPN Site-to-Site** | Túnel IPsec sobre Internet | Variable, económica |
| **AWS Direct Connect** | Enlace dedicado | Baja, predecible |
| **Azure ExpressRoute** | Enlace dedicado | Baja, predecible |

Consideraciones híbridas:

- **DNS** y **identidad** unificados (AD / **Entra ID**).
- **Enrutamiento** (BGP) y direccionamiento **sin solapamiento de CIDR**.
- **Seguridad** en el borde (firewall entre on-prem y cloud).
- **Gateway de VPN** con alta disponibilidad (dos túneles para redundancia).

### 2.6 Multi-Cloud

- Evita dependencia de un solo proveedor (**vendor lock-in**).
- Usa mejores servicios de cada nube.
- **Reto:** gobierno, identidad, red y observabilidad entre nubes; **egress** (salida) es caro.

### 2.7 Seguridad Cloud

- **Modelo de responsabilidad compartida:** el proveedor protege *la nube*; el cliente
  protege *lo que pone en la nube*.
- **IAM:** roles, mínimo privilegio, MFA.
- **Cifrado** en reposo y en tránsito (KMS/Key Vault).
- **WAF**, **DDoS protection**, **seguridad de red** (SG/NSG), centralización de logs.
- Herramientas: AWS GuardDuty, Security Hub; Azure Defender / Sentinel.

---

## 3. Práctica virtual guiada — Arquitectura híbrida Cloud + On-Premise

**Escenario:** *"RetailSur"* tiene su **Data Center on-premise** (sesión 1) con ERP y base de
datos, y quiere **extenderse a AWS** para un portal de e-commerce que debe escalar en
campañas. Requisitos: alta disponibilidad en 2 AZ, acceso seguro a la DB on-premise y salida
de backup a la nube.

### Paso 1 — Definir el direccionamiento

- On-premise: `10.0.0.0/16` (ya usado).
- VPC en AWS: `10.10.0.0/16` (**no solapar** con on-premise).
- Subredes por AZ (2 AZ):

| Subred | CIDR | Tipo |
| --- | --- | --- |
| ecommerce-a | 10.10.1.0/24 | Pública |
| ecommerce-b | 10.10.2.0/24 | Pública |
| app-a | 10.10.11.0/24 | Privada |
| app-b | 10.10.12.0/24 | Privada |
| db-a | 10.10.21.0/24 | Privada |

### Paso 2 — Conectividad híbrida

- **VPN Site-to-Site** (IPsec) entre on-premise y la VPC para el tráfico de la app hacia el
  **ERP on-premise**; dos túneles para redundancia.
- Alternativa premium: **Direct Connect** si el volumen lo justifica.
- **Route tables** con rutas hacia el **Virtual Private Gateway / TGW**.

### Paso 3 — Capa pública y balanceo

- **ALB** en las 2 subredes públicas (capa 7, HTTPS con **ACM**).
- **NAT Gateway** en subred pública para que las subredes privadas salgan a Internet.

### Paso 4 — Capa de aplicación y seguridad

- **Auto Scaling Group** de instancias en subredes privadas (app-a/b).
- **Security Groups**: ALB → app (solo puerto 443); app → DB (solo puerto 5432).
- **NACL** como capa adicional a nivel de subred.

### Paso 5 — Backup y observabilidad

- **S3** (o Azure Blob) con **cifrado** y **versionado** para backups del on-premise vía el
  túnel VPN.
- **CloudWatch** / **Azure Monitor** para métricas, logs y alarmas.

### Diagrama esperado (esquema)

```
   ┌─────────────── On-Premise ────────────────┐
   │  ERP      DB        VPN Gateway (redund.)  │
   └───────────────────────┬────────────────────┘
                           │ IPsec (2 túneles)
   ┌───────────────────────┴───────── AWS ──────────────────────┐
   │  VPC 10.10.0.0/16                                          │
   │  [ALB]──┬── Subred pública A           Subred pública B    │
   │         │                                                  │
   │   App (privada A)  ←→  App (privada B)   [+ ASG]           │
   │         └──── S3 (backups cifrados) ──── NAT GW ── Internet │
   └────────────────────────────────────────────────────────────┘
```

### Entregable

Diagrama draw.io + tabla de subredes (CIDR, tipo, AZ) + lista de **Security Groups** y reglas,
y justificación de la **conectividad híbrida** elegida (VPN vs enlace dedicado).

![Diagrama base de la práctica 3](practicas/sesion-03-arquitectura-hibrida.png)

> **Plantilla base editable:** [`practicas/sesion-03-arquitectura-hibrida.drawio`](practicas/sesion-03-arquitectura-hibrida.drawio)
> · vectorial [`SVG`](practicas/sesion-03-arquitectura-hibrida.svg)
> · abrir en draw.io / diagrams.net y completar.

---

## 4. Ejercicios propuestos

**Ejercicio 1.** Una VPC es `10.20.0.0/16`. ¿Cuántas direcciones caben y cuál es la
notación de la máscara de subred?

**Ejercicio 2.** ¿Diferencia entre **Security Group** y **NACL**? ¿Cuál es stateful?

**Ejercicio 3.** Necesitas balancear **HTTPS con routing por nombre de host**. ¿ALB, NLB o
GWLB?

**Ejercicio 4.** El CIDR on-premise es `192.168.1.0/24`. ¿Puedo usar `192.168.1.0/24` en la
VPC híbrida? Explica.

**Ejercicio 5.** ¿Qué componente permite que una subred **privada** acceda a Internet para
actualizaciones **sin** recibir conexiones entrantes?

---

## 5. Solucionario

**Solución 1.** `10.20.0.0/16` → `2^(32−16) = 2^16 = 65.536` direcciones (menos 5 reservadas
por subred en AWS). La máscara es **255.255.0.0**.

**Solución 2.** El **Security Group** es a nivel **instancia** y es **stateful** (la respuesta
se permite automáticamente). La **NACL** es a nivel **subred** y es **stateless** (hay que
definir reglas de entrada *y* salida). Ambos permiten/dan capas complementarias.

**Solución 3.** **ALB**. El routing por host/path y HTTPS es de capa 7 (aplicación), que es
justo lo que ofrece el Application Load Balancer.

**Solución 4.** **No.** El CIDR no debe solaparse con la red on-premise, porque el
enrutamiento entre ambos se vuelve ambiguo. Hay que usar un rango **distinto**, p. ej.
`192.168.100.0/24` o `10.x.x.x/16`.

**Solución 5.** Un **NAT Gateway** (o NAT instance). Permite tráfico **saliente** desde la
subred privada con traducción de direcciones, bloqueando conexiones entrantes no solicitadas.

---

## 6. Recursos y lecturas sugeridas

- **AWS** — "VPC User Guide", "Well-Architected Framework".
- **Microsoft Learn** — "Azure networking fundamentals", "Virtual Network".
- **AWS Skill Builder / Azure Sandbox** — laboratorios gratuitos.
- Herramienta de diagramas: **draw.io**, **AWS Architecture Icons**, **Azure Icons**.

---

## 7. Autoevaluación (quiz)

1. Diferencia entre IaaS, PaaS y SaaS con un ejemplo cada uno.
2. ¿Qué es una **VPC** y en qué se divide geográficamente?
3. Componente de AWS para salida a Internet desde una subred privada.
4. ¿Qué es el modelo de **responsabilidad compartida**?
5. Equivalente de **Direct Connect** en Azure.

<details>
<summary>Respuestas</summary>

1. IaaS = EC2/VM (tú manejas el SO); PaaS = App Service/RDS; SaaS = Microsoft 365.
2. Red virtual aislada; se divide en **Availability Zones** dentro de una Región.
3. **NAT Gateway**.
4. El proveedor protege *la nube* (infraestructura) y el cliente protege *lo que pone en ella*.
5. **ExpressRoute**.

</details>

---

## 8. Glosario de la sesión

| Término | Definición breve |
| --- | --- |
| **VPC / VNet** | Red virtual privada en AWS / Azure. |
| **Subnet** | Subdivisión de la red, asociada a una AZ. |
| **IGW / NAT GW** | Entrada/salida a Internet / salida desde subred privada. |
| **Security Group / NACL / NSG** | Firewalls cloud (instancia/subred). |
| **ALB / NLB** | Load balancer de capa 7 / capa 4. |
| **Direct Connect / ExpressRoute** | Enlace dedicado a AWS / Azure. |
| **Transit Gateway / Virtual WAN** | Hub de interconexión de redes. |
| **Peering** | Conexión directa entre VPCs/VNets. |
| **Multi-cloud** | Uso de varios proveedores cloud. |
| **Egress** | Tráfico/salida de datos (con costo). |
