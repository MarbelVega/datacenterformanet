# Curso: Data Center, Cloud, Virtualización y Redes Definidas por Software

Curso profesional de **5 sesiones virtuales** (2 horas cada una, 10 horas de clase) que
recorre la cadena completa de la infraestructura de TI moderna: desde el piso físico de un
**Data Center** hasta la **virtualización**, el **cloud networking** y las **redes
programables y multisede**.

**Índice del curso: [empezar por aquí](curso/)**

## Sesiones

| # | Sesión | Práctica virtual |
| --- | --- | --- |
| 1 | [Data Center Profesional](curso/sesion-01-data-center.md) | Diseño conceptual de un Data Center empresarial |
| 2 | [Virtualización con Proxmox y VMware](curso/sesion-02-virtualizacion-proxmox-vmware.md) | Diseño de un servidor virtualizado empresarial |
| 3 | [Cloud Networking: AWS y Azure](curso/sesion-03-cloud-networking-aws-azure.md) | Arquitectura híbrida Cloud + On-Premise |
| 4 | [SDN, NFV y Redes Programables](curso/sesion-04-sdn-nfv-redes-programables.md) | Interpretación de una arquitectura SDN/NFV |
| 5 | [SD-WAN, SASE y Conectividad Multisede](curso/sesion-05-sdwan-sase-multisede.md) | Infraestructura multisede con capas gratuitas (free tier) |

Además: **[Examen final integrador](curso/examen-final.md)** con solucionario, rúbrica y
tabla de fórmulas.

## Estructura del repositorio

- `curso/` — material de las 5 sesiones + examen final.
- `curso/practicas/` — diagramas de práctica en tres formatos: `.drawio` (editable),
  `.svg` (vectorial) y `.png` (2×, para embeber).
- `curso.txt` — programa fuente del curso.

## Publicación (GitHub Pages)

Este repositorio está preparado para publicarse con **GitHub Pages** usando el motor Jekyll:

1. En GitHub, ir a **Settings → Pages**.
2. En **Source**, elegir **Deploy from a branch**.
3. Seleccionar la rama **`main`** y la carpeta **`/ (root)`**.
4. Guardar. El sitio queda disponible en `https://<usuario>.github.io/<repo>/`.

La configuración del sitio vive en [`_config.yml`](_config.yml) (tema `jekyll-theme-cayman` y
plugins estándar de Pages como `jekyll-relative-links`).
