# 🌿 Rumbo Sagrado — Sitio Web de Turismo Consciente y Espiritual

> **Caso de producto real:** Diseño, desarrollo e integración end-to-end de un sitio web para una emprendedora de turismo alternativo, partiendo de una presencia en Instagram y un Linktree, hasta construir un sitio digital completo con captura de leads automatizada, email marketing y SEO técnico.

🔗 **Sitio en producción:** [rumbosagrado.com](https://www.rumbosagrado.com)

---

## ⚡ TL;DR

Diseñé y desplegué un sitio asistido por AI, combinando diseño web, lógica backend custom y automatización con CRM (Go High Level).

- Construcción end-to-end del flujo de usuario (landing → captura → secuencia automatizada)
- Desarrollo de plugin custom en WordPress (PHP, AJAX, controles de seguridad)
- Integración con CRM (Go High Level) y workflows automatizados
- Uso de AI como copiloto para contenido, UX y resolución de problemas

→ **Resultado:** transformación de una presencia estática en redes en un sistema escalable de adquisición

---


## 📌 Contexto del Proyecto

**Cliente:** Emprendedora independiente del sector de turismo consciente/espiritual con comunidad activa en Instagram (viajes grupales y personalizados a destinos sagrados de Argentina y el mundo).

**Punto de partida:**
- Perfil de Instagram con identidad visual consolidada (logo, paleta de colores y tipografía ya definidos y en uso activo)
- Linktree como único punto de contacto externo
- Sin presencia web propia
- Sin sistema para capturar ni dar seguimiento a leads interesados
- Infraestructura preseleccionada: servidor Raiola Networks, licencia Elementor Pro, GoHighLevel como CRM

**Objetivo:** Construir una presencia digital profesional que refleje la identidad de marca existente, centralice la información de servicios y automatice el proceso de captación y seguimiento de clientes.

---

## 🧩 Problema que resolvía

La clienta tenía audiencia pero no infraestructura digital para convertirla en clientes. Un Linktree no permite:
- Comunicar propuesta de valor en profundidad
- Capturar datos de contacto de personas interesadas
- Dar seguimiento sistemático a cada lead


---

## 🛠️ Stack Tecnológico

| Capa | Tecnología | Rol |
|---|---|---|
| **CMS** | WordPress | Base del sitio y gestión de contenido |
| **Constructor visual** | Elementor Pro | Diseño de páginas y layouts |
| **Tema** | Hello Elementor | Tema base liviano optimizado para Elementor |
| **Componentes custom** | HTML / CSS / JS embebido | Funcionalidades fuera del alcance nativo de Elementor |
| **Plugin custom** | `rs-contact-mailer` (PHP) | Formulario de contacto y entrega de regalo con lógica propia |
| **Email transaccional v1** | PHPMailer + SMTP Raiola | Primera versión del envío de mails |
| **Email transaccional v2** | GoHighLevel + Mailgun | Sistema integrado con CRM, SPF + DKIM configurados |
| **CRM + Automatización** | GoHighLevel (GHL) | Captura de leads, pipelines, workflows y email marketing |
| **Hosting** | Raiola Networks | Servidor definido por la clienta |
| **SEO** | Rank Math + Schema JSON-LD | Optimización on-page y datos estructurados |
| **Performance** | LiteSpeed Cache | Caché y optimización de carga |
| **Backups** | UpdraftPlus | Respaldos automáticos |
| **Seguridad** | Solid Security Basic | Hardening del sitio |
| **Diseño de referencia** | Lovable (AI design tool) | Prototipado inicial y generación de imágenes base |

---


## 🎯 Decisiones de Producto

### 1. Respetar la identidad de marca existente
La clienta tenía contenido activo en Instagram con logo, colores y tipografía ya consolidados. Cambiar esa identidad hubiera generado inconsistencia entre su canal principal y el sitio. La decisión fue construir *sobre* lo que ya existía.

### 2. WordPress como plataforma, no como limitación
Con licencia de Elementor Pro y servidor ya contratado, el stack era adecuado para el alcance del proyecto. Proponer una reingeniería tecnológica hubiera sido sobreingeniería sin valor.

### 3. Lovable como punto de partida del diseño
Usar Lovable para generar una base visual rápida redujo el tiempo de prototipado y permitió iterar con la clienta sobre algo tangible desde la primera sesión, en lugar de partir de wireframes en blanco.

---

## ⚙️ Arquitectura del Sistema

### Plugin Custom: `rs-contact-mailer`

Se desarrolló un plugin WordPress a medida para manejar los formularios de contacto y de regalo, dado que las soluciones nativas de Elementor no cubrían los requisitos de seguridad y flexibilidad necesarios.

```
Formulario HTML (front-end)
        │
        ▼
fetch() JS → WordPress admin-ajax.php
        │
        ▼
rs-contact-mailer (plugin PHP)
  ├── Validación de nonce (protección CSRF)
  ├── check_ajax_referer()
  ├── Sanitización doble JS + PHP
  ├── Rate limiting: 1 envío/IP cada 60s (via transients)
  └── PHPMailer → SMTP Raiola
        │
        ▼
Email llega al usuario + copia a contacto@rumbosagrado.com
```

**Estructura de archivos del plugin:**
```
rs-contact-mailer/
├── rs-contact-mailer.php   ← bootstrap: registra actions AJAX, inyecta nonce
├── mailer.php              ← lógica del formulario de contacto
├── mailer-regalo.php       ← lógica del formulario de regalo
└── src/                    ← PHPMailer manual (Exception.php, PHPMailer.php, SMTP.php)
```

---


## 🧲 Arquitectura del Funnel de Conversión (Lead Generation)

Este módulo gestiona la captura de leads mediante un Lead Magnet y la posterior automatización de la secuencia de correos orientada a la venta.

### 🔄 Flujo del Usuario (User Flow)

**1. Opt-in / Formulario**
El usuario navega a la sección de regalos y envía sus datos de contacto (Email, Nombre) a través del Survey de GHL embebido via iframe en Elementor.

**2. Entrega del Lead Magnet**
El envío dispara automáticamente un Workflow en GHL que envía el PDF gratuito por correo electrónico de forma instantánea via Mailgun (`mg.rumbosagrado.com`).

**3. Secuencia de Nutrición (Nurture Sequence)**
El usuario es añadido a una Drip Campaign automatizada con el siguiente flujo:

| Día | Acción | Objetivo |
|---|---|---|
| Día 0 | ✦ Correo de Bienvenida + enlace de descarga del Lead Magnet | Entregar el regalo, generar confianza inicial |
| Día 1 | ✦ Correo de aporte de valor / contexto sobre el problema | Nutrir, posicionar a la clienta como referente |
| Día 3 | ✦ Oferta principal + Call to Action | Conversión a compra |

**Flujo técnico completo:**
```
Usuario completa Survey GHL embebido (página Regalo)
        │
        ▼
GHL captura el contacto automáticamente
        │
        ▼
Workflow "Descarga LM" se dispara
        │
        ├── Agrega tag [FLM-26]-activo
        ├── Chequea si ya recibió el regalo (evita duplicados)
        │
        ├── Si es nuevo → envía Email Nº1 con PDF via mg.rumbosagrado.com
        │       └── Agrega tag entregam7-r20k
        │
        └── Wait 1 hora → continúa Nurture Sequence
                └── Día 0: Bienvenida → Día 1: Valor → Día 3: Oferta + CTA
```

---

## 💡 Aprendizajes clave

- **El primer sistema que funciona no siempre es el correcto.** El formulario con PHPMailer entregaba mails, pero dejaba el CRM ciego. Identificar ese gap y rediseñar fue valioso.
- **Las constraints del cliente son datos, no obstáculos.** El logo y la identidad visual no eran negociables — eso simplificó las decisiones de diseño en vez de complicarlas.
- **Inyección de seguridad.** Nonce, rate limiting y sanitización doble en un formulario de contacto de una pyme son tan necesarios como en cualquier otro contexto.
- **Las herramientas de AI generativa aceleran el prototipado** pero requieren criterio para adaptar el output al contexto real de la marca.

---

## 🗂️ Rol en el proyecto

**Product Builder end-to-end** — Discovery, diseño, desarrollo, integración con GHL, SEO técnico y deploy. Coordinación directa con la clienta, sin equipo externo.

---

## 📸 Vista del Proyecto

🔗 [Ver sitio en producción → rumbosagrado.com](https://www.rumbosagrado.com)

---

## ‍👩‍💻 Autora

**Noelia De Leonardis**
Product Manager · AI Product Builder
[LinkedIn](https://www.linkedin.com/in/noelia-de-leonardis-5b63106) · [GitHub](https://github.com/ndeleonardis)
