---
title: "MIKAN · Registro de cambios aplicados en la web"
author: "David Vallejo Pérez"
email: "david.vallejoperez@emeal.nttdata.com"
date: "2026-10-11"
version: "1.0"
---

# 12. Registro de cambios aplicados en la web

Cambios aplicados en producción el **11 de octubre de 2026**, con el método
empleado y la verificación de cada uno. Las copias del HTML servido antes y
después están en `respaldo-web/antes/` y `respaldo-web/despues/`.

## 12.1 Enlaces del menú *(aplicado)*

Método: API REST de WordPress (`/wp-json/wp/v2/menu-items/<id>`), que es el
mecanismo propio del sistema. Cada operación devolvió HTTP 200 y el valor
resultante.

| ID | Etiqueta | Antes | Después |
|---|---|---|---|
| 1506 | HOME | `http://www.mikan.es/#home` | `https://mikan.es/#home` |
| 1507 | BIOGRAFIA | `http://www.mikan.es/biografia` | `https://mikan.es/biografia/` |
| 1514 | GALERIA | `http://www.mikan.es/galeria` | `https://mikan.es/galeria/` |
| 1515 | CONTACTO | `#Contacto` | `https://mikan.es/contacto/` |

**Verificado** contra el HTML servido: cero apariciones de `href="#Contacto"` y
cero de `http://www.mikan.es`.

Esto resuelve dos puntos de la auditoría: el enlace de contacto que no llevaba a
ninguna página, y la **duplicación de dominio**, que era el problema de
posicionamiento más silencioso del sitio — enlazaba a tres de sus propias
páginas en HTTP sin cifrar y con `www`, repartiendo la autoridad entre dos
dominios distintos.

## 12.2 Enlaces de correo del bloque de contacto *(aplicado)*

El fallo: `mailto:MAILTO:CONTRATACION@MIKAN.ES`, con el prefijo duplicado dentro
del atributo, que en muchos navegadores y clientes de correo no abre nada.

Método: editor de Elementor con su API oficial de comandos
(`$e.run('document/elements/settings')` seguido de `document/save/update`), de
modo que la serialización del documento la hace Elementor y no se manipula el
JSON a mano. Localización del widget **por contenido**, no por identificador.

Hallazgo relevante: **el bloque de contacto está duplicado en las siete
páginas**, cada una con su propia copia y su propio identificador de widget. No
es una plantilla global. Por eso hubo que repetir la operación siete veces:

| Página | Post | Widget | Resultado |
|---|---|---|---|
| Home | 100 | `658ef7f` | OK |
| Biografía | 2189 | `a5d7a2e` | OK |
| Discografía | 1604 | `5dc62ba` | OK |
| Videografía | 1613 | `07c7f47` | OK |
| Prensa | 1675 | `dbe6dba` | OK |
| Galería | 2049 | `e2209e7` | OK |
| Contacto | 2372 | `658ef7f` | OK |

Cambio exacto, con el texto visible intacto para que el aspecto no varíe:

```
antes:    <a href="mailto:MAILTO:CONTRATACION@MIKAN.ES">CONTRATACION@MIKAN.ES</a>
después:  <a href="mailto:contratacion@mikan.es">CONTRATACION@MIKAN.ES</a>
```

**Verificado** en las siete páginas: `mailto:MAILTO:` pasa de 2 apariciones por
página a **0**. Recuento final en el sitio: 28 enlaces correctos a
`contratacion@mikan.es` y 10 a `mikan@mikan.es`, ninguno roto.

### Comprobación de integridad

Comparación del HTML servido antes y después, para descartar pérdidas de
contenido:

| Elemento | Portada antes / después | Videografía antes / después |
|---|---|---|
| Widgets de Elementor | 265 / 265 | 189 / 189 |
| Imágenes | 47 / 47 | 3 / 3 |
| Referencias a YouTube | 34 / 34 | 74 / 74 |
| Enlaces | 61 / 61 | 52 / 52 |

Diferencia de tamaño por página: entre +8 y +58 bytes, coherente con cambiar
solo cadenas de URL. **Ningún elemento perdido.**

## 12.3 Enlace TIENDA de la cabecera *(aplicado)*

El enlace apuntaba a `http://www.instagram.com/mikanoficial` —a Instagram en
lugar de a la tienda, y en HTTP sin cifrar— mientras el menú de WordPress ya
apuntaba correctamente a la tienda.

Discriminador usado: el enlace defectuoso empleaba `http://`, mientras los
iconos sociales legítimos usan `https://`. Eso permitió sustituir únicamente
esa cadena exacta sin tocar los iconos de Instagram.

Hallazgo: el enlace **no estaba en un widget de menú sino en un widget de
texto**, uno distinto por página. Es decir, la navegación secundaria de la
cabecera está escrita a mano en HTML dentro de un bloque de texto en lugar de
ser un menú de WordPress.

| Página | Post | Widget |
|---|---|---|
| Home | 100 | `78b7d5a` |
| Biografía | 2189 | `bd8be20` |
| Discografía | 1604 | `ef6844a` |
| Videografía | 1613 | `ee56cdc` |
| Prensa | 1675 | `80ddd96` |
| Galería | 2049 | `9493f1d` |
| Contacto | 2372 | `e9cc6ef` |

**Verificado** en la web pública: TIENDA apunta a `https://mikanstore.es/` en
sus dos apariciones (cabecera y menú móvil), cero apariciones de
`http://www.instagram.com/mikanoficial`, y los tres iconos sociales de
Instagram siguen intactos con su `https://`.

## 12.4 Pendiente

| Punto | Estado | Qué falta |
|---|---|---|
| Icono de **Spotify** | Pendiente | Hace falta la URL del **perfil de artista** (`open.spotify.com/artist/...`). Hoy apunta a un álbum. |
| Icono de **Apple Music** | Pendiente | Hace falta la URL de su **página de artista**. Hoy es un redirector de YouTube con token caducable que apunta a un single de 2022. |
| Enlazar el **EPK** | Pendiente | `Mikan-Amor-Sin-Final-EPK2026.pdf` está subido sin enlazar. |
| Sección de **prensa y contratación** | Pendiente | Trabajo de creación de página, no de corrección. |
| Sección de **fechas** | Pendiente | Ídem. |
| Vídeos de **directo en Videografía** | Pendiente | Añadir las tres piezas y la gala de RTVE. |

## 12.5 Recomendación derivada de lo anterior

Dos bloques que deberían ser plantillas únicas están duplicados en las siete
páginas: el de contacto y la navegación secundaria de la cabecera, esta última
escrita además a mano en HTML dentro de un widget de texto. Significa que
**cualquier cambio futuro de teléfono, de correo o de menú obliga a siete
ediciones**, con el riesgo de que una se quede atrás y queden datos
inconsistentes. Es exactamente el mecanismo por el que los `mailto` llevaban
rotos quién sabe cuánto tiempo. Convertirlo en una plantilla de pie de página
de Elementor es media hora de trabajo y elimina esa clase de error de forma
permanente. Mismo razonamiento para el bloque de cabecera.
