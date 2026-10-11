---
title: "MIKAN · Archivos web"
author: "David Vallejo Pérez"
email: "david.vallejoperez@emeal.nttdata.com"
date: "2026-10-11"
version: "1.0"
---

# Archivos web

## `contratacion.html`

Contenido maquetado de la página de contratación, pensado para pegarse en un
widget **HTML** de Elementor en `mikan.es/contratacion/`. Es autónomo: lleva sus
propios estilos con prefijo `mk-`, de modo que no interfiere con el tema ni el
tema con él.

Decisiones técnicas:

- **Vídeos en `youtube-nocookie.com`**, para que el gestor de consentimiento de
  cookies no los oculte a quien no acepta seguimiento. En la página donde los
  vídeos son el argumento principal, que un bloqueador los esconda sería el peor
  resultado posible.
- **Carga diferida** en los tres vídeos: no pesan hasta que se llega a ellos.
- **Instagram enlazado como texto**, nunca embebido: un embebido carga el
  JavaScript de Instagram, ralentiza la página, se rompe si el post desaparece y
  se lleva al visitante fuera de la web justo cuando estaba decidiendo.
- **Diseño adaptable** sin dependencias externas, con rejillas que se recolocan
  solas en móvil.

### Material enlazado y material que falta

Enlazado:

- `Mikan-Amor-Sin-Final-EPK2026.pdf`, bajo el nombre honesto de «Dossier de
  prensa». Es un EPK **de un single**: sirve para prensa, no para contratación.

**Pendiente de producir**, y es ahora el cuello de botella real:

| Material | Estado | Cómo resolverlo |
|---|---|---|
| **Dossier de contratación** | No existe | Debe incluir formatos, duraciones, ficha técnica y personas en gira |
| **Rider técnico** | No existe | Una hora con `plantillas/rider-tecnico.md` |
| **Fotos en alta (ZIP)** | No existe | Subir 6-8 en tamaño original y comprimir. La galería sirve miniaturas de 215 px, inservibles para prensa |

Los dos PDF de 2021 que hay en la biblioteca (`MIKAN-PRESSKIT-2021` y
`MIKAN-BIO-PRESSKIT-2021`) **no deben enlazarse**: son de hace cinco años y no
incluyen la gala de RTVE, el premio de 2025 ni la candidatura al Benidorm Fest.
Un dossier desactualizado resta más que la ausencia de dossier.

### Dato que falta antes de publicar

El número de **personas en gira** por formato. Hoy la tabla de formatos remite
al dossier para ese dato, lo cual es aceptable, pero tenerlo en la tabla evita
un correo de ida y vuelta con cada promotor.
