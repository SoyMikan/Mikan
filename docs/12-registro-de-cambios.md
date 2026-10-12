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

## 12.5 reCAPTCHA del formulario de contacto *(estaba roto, parcialmente arreglado)*

**Aviso:** una versión anterior de este documento afirmaba que el reCAPTCHA
funcionaba y que no debía tocarse. **Era incorrecto.** El formulario llevaba
roto un tiempo indeterminado. Se deja constancia del error de diagnóstico
porque la forma de equivocarse es instructiva.

### El fallo

El campo estaba configurado en Elementor como **v2 casilla**, pero la clave de
sitio es de tipo **basado en puntuación**. Google carga el widget, detecta el
desajuste de tipo y, en lugar de la casilla, muestra dentro del iframe:

> **ERROR for site owner: Invalid key type**

Sin casilla no hay token, y sin token Elementor rechaza el envío. **Nadie podía
enviar el formulario.**

### Por qué el diagnóstico tardó tres intentos

Todas las señales indirectas daban verde y aun así la conclusión era falsa:

| Señal comprobada | Resultado | Por qué engañaba |
|---|---|---|
| Peticiones de red a Google | Todas 200 | El widget carga bien; el error es de contenido |
| Dimensiones del contenedor | 304×78, normal | El error ocupa el mismo espacio que la casilla |
| Consola del navegador | Sin errores | Google no registra este fallo en consola |
| Clave secreta contra `siteverify` | `invalid-input-response` | El secreto es válido; falla el tipo de clave |
| Envío con token falso | Rechazado correctamente | La validación funciona, el problema es anterior |

El error vive **dentro de un iframe de otro dominio** y no se puede leer por
código. Solo se ve con una **captura de pantalla** del widget.

**Lección de método:** ante un componente de terceros embebido en iframe, la
captura de pantalla va primero, no al final. Las señales indirectas pueden ser
todas correctas y la conclusión falsa, porque describen capas distintas de
aquella donde está el fallo.

### Lección operativa para el proyecto

Un formulario de contratación que no envía es un **fallo silencioso**: no avisa
al visitante de forma evidente, no notifica a nadie y no deja rastro. Igual que
los `mailto` mal formados. Es la segunda vez en el mismo sitio.

**Recomendación: probar los canales de contacto una vez al mes.** Un envío por
el formulario y un correo a contratación desde una cuenta ajena. Dos minutos, y
es la diferencia entre detectar un corte en treinta días o en nueve meses.

### El arreglo aplicado

La pantalla de ajustes de Elementor tiene **dos pares de campos de claves**:

```
elementor_pro_recaptcha_site_key        <- v2
elementor_pro_recaptcha_secret_key      <- v2
elementor_pro_recaptcha_v3_site_key     <- v3
elementor_pro_recaptcha_v3_secret_key   <- v3
elementor_pro_recaptcha_v3_threshold
```

Las claves, que son de tipo puntuación, estaban en el par **v2**. Un campo de
diferencia, y el formulario llevaba meses sin poder enviarse.

Cambios aplicados el 11/10/2026:

1. Campo del formulario de `recaptcha` a `recaptcha_v3` (post 2372, widget
   `afcefbc`).
2. Clave de sitio y secreto trasladados al par **v3** en
   `admin.php?page=elementor-settings`. **Nota:** el aviso que muestra Elementor
   indica la ruta «Elementor → Settings → Integrations», pero la URL
   `admin.php?page=elementor-integrations` no existe en esta instalación y
   devuelve error. La pantalla buena es `admin.php?page=elementor-settings`.

### Verificación: las tres capas, comprobadas por separado

No basta con que el formulario envíe: antes de poner las claves también enviaba,
sin protección alguna. Las tres propiedades se verificaron de forma
independiente:

| Capa | Prueba | Resultado |
|---|---|---|
| El formulario envía | Envío real con token genuino generado en la página | `success: true` |
| El captcha protege | Envío con token falso, imitando a un bot | Rechazado: «reCAPTCHA V3 validation failed, suspected as abusive usage» |
| El correo llega | Confirmación del destinatario | Recibido |

Un formulario sin protección y uno protegido se comportan igual para un
visitante normal. Solo se distinguen poniéndose en el lugar del atacante.

### Estado final

**Canal de contacto operativo y protegido.** Umbral de puntuación en el valor
por defecto; si entrara spam, bajarlo, y si bloqueara a personas reales,
subirlo, en el mismo campo `elementor_pro_recaptcha_v3_threshold`.

## 12.6 Pendiente: barrido de los 21 bloques de iconos

Cada página tiene **tres** bloques de iconos sociales, 21 en total, con
contenido divergente entre sí. Estado y cambios acordados:

| Cambio | Alcance |
|---|---|
| Spotify al perfil de artista `open.spotify.com/artist/3WZitLJsCzDpVGtqTD4M0J` | Roto (apunta a un álbum) en 15 de los 21 |
| Apple Music fuera | Incluido el redirector de YouTube caducado que queda en la portada |
| TikTok en el tercer bloque de cada página | Falta en 7 de los 21 |
| Twitter/X fuera | Presente en los 21 |

Resultado buscado: fila idéntica en las siete páginas —YouTube, Spotify,
Instagram, Facebook, TikTok y correo—, sin enlaces rotos ni perfiles
inactivos.

**No aplicado.** La automatización fue denegada por el control de permisos del
entorno, por tratarse de un borrado de elementos en un sitio en producción.

**Vía manual más rápida que editar 21 bloques uno a uno:** dejar **un** bloque
perfecto, copiarlo con clic derecho → Copiar, y pegar encima de los otros 20 con
Pegar estilo o reemplazando el widget. Elementor conserva la estructura y asigna
identificadores nuevos.

**Vía estructural, la que de verdad conviene:** convertir cabecera y pie en
**plantillas globales** de Elementor. Hoy el mismo bloque vive duplicado 21
veces, y la navegación secundaria está además escrita a mano en HTML dentro de
widgets de texto. Eso convierte cualquier cambio de un dato de contacto o de un
enlace en 21 ediciones, y es el mecanismo exacto por el que los `mailto`
llevaban rotos mucho tiempo y por el que el cambio de TikTok revirtió a medias
la corrección de Spotify. Pasar a plantilla única es media hora y elimina esta
clase de error de forma permanente.

## 12.7 Recomendación derivada de lo anterior

Dos bloques que deberían ser plantillas únicas están duplicados en las siete
páginas: el de contacto y la navegación secundaria de la cabecera, esta última
escrita además a mano en HTML dentro de un widget de texto. Significa que
**cualquier cambio futuro de teléfono, de correo o de menú obliga a siete
ediciones**, con el riesgo de que una se quede atrás y queden datos
inconsistentes. Es exactamente el mecanismo por el que los `mailto` llevaban
rotos quién sabe cuánto tiempo. Convertirlo en una plantilla de pie de página
de Elementor es media hora de trabajo y elimina esa clase de error de forma
permanente. Mismo razonamiento para el bloque de cabecera.

## 12.8 Gestiones externas

| Fecha | Gestión | Estado |
|---|---|---|
| 12 oct 2026 | **DM a `@santanderfestejos`** (Ayuntamiento de Santander) pidiendo corregir «Milán» → «MIKAN» en el pie del vídeo de TikTok del Orgullo del Río de la Pila, `.../video/7521487556484042006` | **Enviado por el artista.** Pendiente de respuesta |

**Seguimiento previsto:** no reclamar antes de 72 horas. Si a 15 de octubre no
hay respuesta, enviar la versión de correo formal a la Concejalía de Festejos o
de Cultura, que deja registro administrativo.

**Mientras no esté corregido**, el enlace de prueba que se adjunta a la nota de
prensa de GPS17 y al dosier de contratación es **el reel de Instagram**, donde
el crédito ya está bien puesto, no el de TikTok.

**Si lo corrigen:** agradecerlo en público, en una story etiquetando a la
cuenta. Cuesta treinta segundos, deja a la institución en buen lugar y crea la
relación que hace falta para el siguiente contacto —que es el del día 18 de la
campaña de GPS17, pidiéndoles difusión de la votación—. Esas dos peticiones no
deben ir nunca en el mismo mensaje: **una queja y un favor juntos se anulan.**
Primero se cierra la corrección, después se agradece, y solo entonces se pide.

**Y el pie erróneo queda registrado literal en `docs/16-credenciales.md`**, así
que cuando se corrija no se pierde la constancia de lo que decía.
