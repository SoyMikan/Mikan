---
title: "MIKAN · Guía paso a paso de los cambios en la web"
author: "David Vallejo Pérez"
email: "david.vallejoperez@emeal.nttdata.com"
date: "2026-10-11"
version: "1.0"
---

# 11. Guía paso a paso de los cambios en la web

Guía de ejecución de las correcciones de [`09-auditoria-web.md`](09-auditoria-web.md),
escrita para hacerlas uno mismo sin conocimientos técnicos. El sitio presenta
indicios de estar construido sobre **WordPress** (estructura de URLs, imágenes
`-scaled`, gestor de consentimiento de cookies), así que las rutas de menú son
las de WordPress. Si el tema usa un constructor visual (Elementor, WPBakery,
Divi), algunos elementos se editan desde el constructor en lugar del editor
estándar; se indica dónde puede pasar.

## 11.0 Antes de tocar nada *(10 minutos, no saltar)*

1. **Cambiar la contraseña de administrador.** Si una contraseña se ha enviado
   alguna vez por chat, correo o mensaje, hay que considerarla comprometida.
   `Usuarios → Perfil → Nueva contraseña → Generar`. Y activar la verificación
   en dos pasos si el alojamiento lo permite.
2. **Hacer copia de seguridad.** Dos vías:
   - Desde el panel del alojamiento: buscar "copias de seguridad" o "snapshots"
     y crear una manual.
   - O instalar un plugin de respaldo y lanzar una copia completa antes de
     empezar.
3. **Anotar la hora de la copia.** Si algo se rompe, restaurar a ese punto y
   volver a empezar. Con copia, ningún error de esta guía es irreversible.
4. **No editar archivos de tema ni de código.** Todo lo de esta guía se hace
   desde la interfaz. Si algún paso parece exigir tocar código, es mejor
   detenerse y preguntar.

---

## 11.1 P0 · Los enlaces de correo *(5 minutos, máxima prioridad)*

El problema: los enlaces contienen el prefijo duplicado, `mailto:MAILTO:...`, y
por eso pueden no abrir nada.

**Dónde está:** en la sección de contacto, que aparece al final de la página de
inicio y repetida en el pie de otras páginas. Según cómo esté montado el tema,
puede estar en uno de estos tres sitios —hay que revisarlos en este orden:

1. `Páginas → Inicio (o Home) → Editar`, y bajar hasta el bloque de contacto.
2. `Apariencia → Widgets`, en las áreas de pie de página.
3. Si el tema usa constructor visual: `Editar con Elementor` (o el que sea) en
   la página de inicio, y buscar el bloque de contacto.

**Qué hacer:** localizar el texto `CONTRATACION@MIKAN.ES`, hacer clic en él y
abrir el campo de enlace. Verás algo parecido a:

```
MAILTO:CONTRATACION@MIKAN.ES
```

Sustituirlo por exactamente esto, en minúsculas:

```
mailto:contratacion@mikan.es
```

**La causa del fallo, para no repetirlo:** el campo de enlace del editor ya
añade `mailto:` por su cuenta cuando detecta un correo. Al escribir dentro
"MAILTO:...", queda duplicado. Así que **o se escribe solo el correo**
(`contratacion@mikan.es`) **o se escribe la forma completa una sola vez**
(`mailto:contratacion@mikan.es`). Nunca las dos cosas.

Repetir con `mikan@mikan.es`.

**Mejora adicional, 1 minuto:** dejar el correo también como **texto visible**
junto al enlace. Mucha gente copia la dirección en lugar de pulsar, y así
funciona igual aunque el enlace falle.

**Cómo comprobar que ha quedado bien:** guardar, abrir la web en una ventana
privada, pulsar el enlace. Debe abrirse el cliente de correo con el destinatario
ya puesto. Si no pasa nada, el enlace sigue mal.

## 11.2 P0 · Los iconos de Spotify y Apple Music *(15 minutos)*

**Dónde está:** son los iconos de la barra superior y del pie. Normalmente en
`Apariencia → Personalizar` (buscar "Cabecera", "Redes sociales" o "Social
icons"), o en las opciones del tema. Si el tema trae constructor de cabecera,
estará ahí.

**Spotify.** El enlace actual lleva a un álbum concreto:

```
ACTUAL:   https://open.spotify.com/album/6AXQELOlYZsiLc3UtSggly?si=...&nd=1
CORRECTO: https://open.spotify.com/artist/[SU-ID-DE-ARTISTA]
```

Cómo obtener la URL correcta: abrir Spotify, entrar en **su perfil de artista**
(no en un álbum), menú `...` → `Compartir` → `Copiar enlace del artista`. Al
pegarlo, **borrar todo lo que vaya después de `?`**, incluidos `?si=` y `&nd=`:
son identificadores de sesión y no deben ir en un enlace público permanente.

Esto importa más de lo que parece: hoy todo el tráfico que la web manda a
Spotify aterriza en un álbum suelto, donde no hay botón de seguir al artista ni
acceso al catálogo.

**Apple Music.** El enlace actual es un redirector de YouTube con token
caducable que apunta a un single de 2022 — es decir, está condenado a dejar de
funcionar, si no lo ha hecho ya. Sustituir por la URL de su **página de artista**
en Apple Music, que tiene esta forma:

```
https://music.apple.com/es/artist/[nombre]/[id]
```

## 11.3 P0 · Los enlaces de los menús *(10 minutos)*

**Dónde:** `Apariencia → Menús`.

Hay varios menús (principal, secundario, móvil, pie). Hay que revisarlos todos
con el selector de la parte superior, porque los errores están en menús
distintos.

| Elemento | Valor actual | Valor correcto |
|---|---|---|
| **TIENDA** (cabecera) | `http://www.instagram.com/mikanoficial` | `https://mikanstore.es/` |
| **CONTACTO** (principal) | `#Contacto` | `https://mikan.es/contacto/` |

En el caso de TIENDA, el menú móvil ya apunta bien a `mikanstore.es`: el error
está solo en el de cabecera. Y conviene que todos los enlaces usen `https://`,
nunca `http://`.

## 11.4 P1 · Subir la gala de RTVE a portada y a Videografía *(30 minutos)*

Es el cambio con más retorno después de los enlaces: hoy su mejor material
comercial está enterrado dentro del cuerpo de texto de una nota de prensa.

**El vídeo:** `https://www.youtube.com/watch?v=AOzOP0m50Vo` — está en el canal
oficial de **RTVE Música**, con 155.084 visualizaciones. Que esté en un canal
institucional ajeno es precisamente lo que le da valor: no es él diciendo que
actuó en TVE, es TVE publicándolo.

**En la portada:** `Páginas → Inicio → Editar`, añadir un bloque de vídeo en la
parte alta de la página (por encima de la nota de prensa) y pegar la URL. En
WordPress, pegar una URL de YouTube en una línea vacía genera el vídeo
incrustado automáticamente.

Encima del vídeo, un titular sobrio del tipo:

> **Gala de Nochevieja de RTVE La 1**

Sin adjetivos. El dato ya es la credencial.

**En Videografía:** `Páginas → Videografía → Editar`. Añadir **tres entradas
nuevas que hoy no existen en la web**, y colocarlas **antes** de los videoclips:

| Orden | Pieza | URL |
|---|---|---|
| 1 | Gala de Nochevieja de RTVE La 1 — "Espectral" (2026) | `youtube.com/watch?v=AOzOP0m50Vo` |
| 2 | The Mikan Show · La Rentrée Tour (directo) | `youtu.be/dmHiz-7m71A` |
| 3 | The Mikan Show · Sauvage Tour (directo) | `youtu.be/cMG_DKOqTzc` |
| 4 | La Rentrée Tour (vídeo promocional) | `youtu.be/ZCIe503yf8Q` |

Al pegar enlaces de YouTube, **quitar todo lo que vaya después de `?`**
(`?si=`, `?is=`): son identificadores de sesión.

Y de paso, en la misma edición:
- **Eliminar la entrada duplicada** de "Filias y fobias", que aparece dos veces.
- **Añadir el año** a cada pieza. Sin fecha, no se distingue lo nuevo de 2015.

## 11.5 P1 · Crear la sección "Prensa y contratación" *(2-3 horas)*

Es la sección que hoy no existe y que hace que el material no sea alcanzable.

1. `Páginas → Añadir nueva`. Título: **Prensa y contratación**.
2. `Medios → Añadir nuevo`: subir el **dossier en PDF**, el **rider** y un
   **ZIP con 6-8 fotos en alta resolución**. Copiar la URL de cada archivo
   subido.
3. En la página, crear enlaces de descarga claros:
   - Dossier de contratación (PDF)
   - Rider técnico (PDF)
   - Fotos de promoción en alta resolución (ZIP)
   - Vídeo de directo (enlace a YouTube)
   - One-sheet (PDF)
4. Añadir al final los datos de contratación como **texto visible**: correo,
   teléfono y formulario.
5. `Apariencia → Menús`: añadir la página al **menú principal**. No al pie: al
   menú principal. Un promotor no busca en el pie.

**Sobre la galería:** las 40 imágenes actuales se quedan como están para el
público —se sirven a 215 px, que vale para mirar pero no para publicar—. Lo que
hace falta es el ZIP aparte con 6-8 imágenes en alta. Un periodista que no
encuentra imagen publicable no publica, y no escribe para pedirla.

## 11.6 P1 · Crear la sección de fechas *(1 hora)*

1. `Páginas → Añadir nueva`. Título: **Conciertos** o **Fechas**.
2. Mientras no haya calendario, **no dejarla vacía ni poner "Próximamente"**.
   Poner dos cosas:
   - Un formulario de aviso: "Déjame tu correo y te aviso de las próximas
     fechas".
   - Un bloque de contratación con enlace a Prensa y contratación.
3. Quitar el "Próximamente" de la portada y enlazar a esta página.

El "Próximamente" sin fechas comunica tres cosas malas a la vez: al fan, que no
hay nada; al promotor, que nadie le está contratando; y al buscador, que la
página no se actualiza.

## 11.7 P2-P3 · Resto *(cuando haya tiempo)*

- **Newsletter:** alta en un servicio gratuito, y formulario en portada, en el
  pie y en la sección de fechas.
- **Dominio canónico:** `Ajustes → Generales`. Dejar la misma forma en
  "Dirección de WordPress" y "Dirección del sitio" —con `www` o sin él, pero la
  misma— y usar solo esa en todos los enlaces internos. Hoy el sitio mezcla
  `mikan.es` y `www.mikan.es` en sus propios menús, y eso divide el
  posicionamiento.
- **Título y descripción:** `Ajustes → Generales` y el plugin de SEO. Poner
  `MIKAN · Cantante y compositor de pop`, nunca "Mikan" a secas: el nombre
  colisiona con un cítrico japonés y con artistas homónimos, y es la causa
  principal de que no sea encontrable.
- **Sustituir los superlativos por pruebas enlazadas** en la nota de prensa de
  portada. Ahora que el vídeo de RTVE está arriba con su cifra, los adjetivos
  no verificables solo restan credibilidad.

---

## 11.8 Si se quiere delegar el trabajo técnico

Tres vías, en orden de preferencia:

1. **Hacerlo con esta guía.** Los pasos 11.1 a 11.3 son **30 minutos** y son los
   que mueven la aguja. El resto puede esperar una semana sin coste.
2. **Encargarlo a un profesional** con esta guía como pliego. Es un encargo de
   2-4 horas de trabajo para alguien que conozca WordPress, y tener el alcance
   escrito evita pagar de más.
3. **Trabajar sobre una copia.** Si se quiere revisión o desarrollo más
   profundo, la vía ordenada es exportar el contenido del sitio y volcarlo en
   este repositorio, y trabajar ahí en lugar de en producción. Es la única
   manera de probar cambios sin riesgo.

**En ninguna de las tres hace falta compartir contraseñas por chat o correo.**
Si hubiera que dar acceso a alguien, lo correcto es `Usuarios → Añadir nuevo`
con un usuario propio para esa persona, con el rol mínimo necesario —`Editor`
basta para casi todo lo de esta guía—, y eliminarlo al terminar. Así el acceso
es trazable, revocable y no obliga a nadie a compartir su propia contraseña.
