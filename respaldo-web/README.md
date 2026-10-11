---
title: "MIKAN · Copias previas de la web"
author: "David Vallejo Pérez"
email: "david.vallejoperez@emeal.nttdata.com"
date: "2026-10-11"
version: "1.0"
---

# Copias previas de mikan.es

HTML servido de las siete páginas públicas, descargado el **11 de octubre de
2026 antes de aplicar cualquier cambio**. Sirve como registro del estado
original y permite comparar y revertir cualquier enlace.

No es una copia de seguridad del sitio: no incluye base de datos, medios ni
configuración. La copia de seguridad completa se hace desde el panel del
alojamiento.

## Estado registrado de los enlaces de correo

Recuento de apariciones en las siete páginas:

| Enlace | Apariciones | Estado |
|---|---|---|
| `mailto:MAILTO:CONTRATACION@MIKAN.ES` | 7 | **Roto** |
| `mailto:MAILTO:MIKAN@MIKAN.ES` | 7 | **Roto** |
| `mailto:contratacion@mikan.es` | 21 | Correcto |
| `mailto:mikan@mikan.es` | 3 | Correcto |

Las siete apariciones de cada enlace roto corresponden a **un único widget de
texto de Elementor** (`data-id="658ef7f"`) incluido en una plantilla global, que
se repite en todas las páginas. Contenido original exacto del widget:

```html
<p><b>INFORMACIÓN:</b></p>
<p><a href="mailto:MAILTO:CONTRATACION@MIKAN.ES">CONTRATACION@MIKAN.ES</a> | <a href="mailto:MAILTO:MIKAN@MIKAN.ES">MIKAN@MIKAN.ES</a></p>
<p>TEL. (+34) 603. 55. 12. 98.</p>
```

Los 21 enlaces correctos son los iconos de sobre de la cabecera y del pie, que
sí funcionan. Es decir: **el enlace que falla es justamente el del bloque de
contacto**, el que un promotor usaría.
