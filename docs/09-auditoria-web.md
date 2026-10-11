---
title: "MIKAN · Auditoría de mikan.es"
author: "David Vallejo Pérez"
email: "david.vallejoperez@emeal.nttdata.com"
date: "2026-10-11"
version: "1.0"
---

# 9. Auditoría de mikan.es

Auditoría de las secciones Home, Biografía, Discografía, Videografía, Prensa,
Galería y Contacto, realizada el **11 de octubre de 2026** sobre el sitio público.

## 9.1 Veredicto

La web **no es el problema que parecía**. Tiene buena estructura, catálogo bien
organizado, archivo de prensa extenso, galería con 40 imágenes y 13 vídeos
publicados. Hay mucho más material del que se veía a primera vista.

El problema es otro, y es más barato de arreglar: **el material existe pero no
está donde se busca, y el embudo de contacto está roto.** Hay cuatro enlaces
defectuosos, incluido —y esto es lo grave— **el enlace de correo de contratación**.

## 9.2 P0 · Roto. Arreglar hoy *(10 minutos)*

### 1. Los enlaces de correo no funcionan 🔴

En la sección de contacto, los enlaces están mal formados:

```
INCORRECTO:  mailto:MAILTO:CONTRATACION@MIKAN.ES
INCORRECTO:  mailto:MAILTO:MIKAN@MIKAN.ES

CORRECTO:    mailto:contratacion@mikan.es
CORRECTO:    mailto:mikan@mikan.es
```

El prefijo `MAILTO:` está duplicado dentro del propio atributo. En muchos
navegadores y clientes de correo, **ese enlace no abre nada**: el visitante hace
clic y no pasa absolutamente nada.

Consecuencia práctica, dicha sin dramatismo pero sin suavizarla: **es posible que
promotores, periodistas o programadores hayan intentado escribir y no hayan
podido.** No hay forma de saber cuántos ni desde cuándo. Es el arreglo con mayor
retorno por minuto invertido de todo el plan.

Además, conviene escribir el correo también **como texto visible y copiable**,
no solo como enlace. Mucha gente copia la dirección en lugar de hacer clic.

### 2. El enlace de Apple Music está condenado a caducar 🔴

```
ACTUAL:  https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhq...&q=https%3A%2F%2Fmusic.apple.com%2F...%2Fcontéstame-single%2F1619965965
```

Es un **redirector de YouTube con token temporal**, no un enlace de Apple Music.
Esos tokens caducan, así que el enlace dejará de funcionar (si no lo ha hecho
ya). Y además apunta a un single de **2022**, no al perfil del artista.

Sustituir por la URL directa de su **página de artista** en Apple Music.

### 3. El enlace de Spotify va a un álbum, no a su perfil 🔴

```
ACTUAL:  https://open.spotify.com/album/6AXQELOlYZsiLc3UtSggly?si=...&nd=1
```

Esto es relevante para algo que él mismo ha detectado: que "Spotify no le
funciona". **Parte de la explicación está aquí.** Todo el tráfico que envía su
web a Spotify aterriza en un álbum concreto, donde no hay botón de seguir al
artista ni acceso al resto del catálogo. Nadie sigue a un artista al que no
llega.

Sustituir por `https://open.spotify.com/artist/[ID]` y **quitar los parámetros
`?si=` y `&nd=`**, que son de seguimiento de sesión y no deben ir en un enlace
público permanente.

### 4. "Tienda" apunta a dos sitios distintos 🟡

```
Cabecera:     http://www.instagram.com/mikanoficial   ← Instagram, y en http
Menú móvil:   https://mikanstore.es/                  ← correcto
```

Unificar a `https://mikanstore.es/`. El `http://` sin cifrado además lanza avisos
de seguridad en algunos navegadores.

### 5. "Contacto" lleva a un ancla y a una página distinta 🟡

El menú principal apunta a `#Contacto` (ancla de la página de inicio) mientras
existe una página real en `/contacto/`. Unificar a la página.

### 6. Dominio inconsistente 🟡

El sitio mezcla `mikan.es` y `www.mikan.es` en los propios menús. Elegir una
versión canónica, redirigir la otra por 301 y usar solo la elegida en todos los
enlaces internos. Afecta directamente al posicionamiento.

## 9.3 P1 · El material existe, hay que exponerlo *(1-2 días)*

### 7. El dossier en PDF no está enlazado en ninguna parte

Se han revisado Home, Biografía, Discografía, Videografía, Prensa, Galería y
Contacto: **no hay ningún enlace a un PDF en ninguna sección navegable.**

Si el documento existe —y el artista confirma que existe—, entonces hoy es un
archivo que solo él sabe que está ahí. Para cualquier efecto comercial,
**material no enlazado = material inexistente.**

**Acción:** crear una sección **"Prensa y contratación"** accesible desde el menú
principal, con descargas visibles. No en el pie, no dentro de un párrafo: en el
menú.

### 8. La galería no sirve para prensa

Hay 40 imágenes, lo cual es un buen archivo. Pero se sirven en **miniaturas de
215 × 215 píxeles** y los archivos son versiones reducidas (`-scaled`), sin
opción de descarga en alta resolución.

Un periodista que no encuentra una imagen publicable **no publica**, y no
escribe para pedirla. Esto es tráfico de prensa perdido de forma silenciosa.

**Acción:** mantener la galería como está para el público, y añadir aparte un
paquete de **6-8 imágenes en alta resolución** descargables en un ZIP, con
créditos del fotógrafo en el nombre de archivo.

### 9. Falta la sección de fechas

No hay sección de conciertos en la navegación. **Aunque hoy no haya fechas, debe
existir**, con las dos funciones que cumple: informar cuando haya calendario, y
mientras no lo haya, captar correos ("avísame de las próximas fechas") y
canalizar la contratación.

## 9.4 P2 · El activo mejor escondido *(2 horas)*

### 10. Su mejor vídeo de venta está enterrado en un párrafo

Este es el hallazgo más importante de la auditoría después de los enlaces rotos.

La actuación de la **gala de Nochevieja de RTVE La 1** está publicada en YouTube
(`youtube.com/watch?v=AOzOP0m50Vo`), y **es su mejor material comercial
existente**: televisión pública, prime time, directo, puesta en escena y
producción que él no tuvo que pagar.

Hoy ese enlace está **dentro del cuerpo de texto de una nota de prensa en la
página de inicio.** No está en Videografía. No está en Prensa como pieza
destacada. No está en portada como vídeo principal. Lo mismo ocurre con la
actuación en el **Festival Horteralia** y con **Los conciertos de Los40**, ambos
enlazados solo como posts de Instagram dentro de ese mismo texto.

**Acción:** la actuación de RTVE debe ser **el primer vídeo de la portada y el
primer vídeo de Videografía**, con rótulo explícito del tipo "Gala de Nochevieja
de RTVE La 1". Y debe ser el enlace que encabece todos los correos de booking.

### 11. Videografía está ordenada por catálogo, no por utilidad

Hay 13 entradas, y conviene saber qué son exactamente, porque cambia el plan:

| Tipo | Cantidad |
|---|---|
| Videoclips oficiales | 8 |
| Videolyrics | 4 |
| **Vídeos de directo o actuación** | **0** |

Aquí hay un matiz que conviene precisar, porque el artista menciona tener vídeos
para vender el show: **un videoclip vende la canción; un vídeo de directo vende
el concierto.** Son dos productos distintos y los compra gente distinta. Un
programador de fiestas que ve un videoclip no sabe qué va a pasar en su plaza;
necesita ver escenario, público y sonido en vivo.

Lo publicado hoy en Videografía, siendo buen material, **no es material de
booking**. Si existe grabación de directo sin publicar —de la gala, de
Horteralia, de Los40 o del show completo—, **ese es el material que falta
exponer**, y es prioritario sobre cualquier otra cosa.

**Y falta lo más importante:** existen **tres piezas de directo publicadas en
YouTube** —la gala de RTVE, *The Mikan Show Sauvage Tour (Live)* y *The Mikan
Show (La Rentrée Tour)*, esta última con **111.318 visualizaciones**— y
**ninguna de las tres aparece en Videografía**. El material que vende el show
está fuera de la web; el que no lo vende ocupa la sección completa. Inventario
con cifras en [`10-inventario-audiovisual.md`](10-inventario-audiovisual.md).

**Acciones:**
- **Añadir las tres piezas de directo**, que hoy no están.
- Reordenar: directo y TV primero, videoclips después, videolyrics al final.
- Corregir la **entrada duplicada** de "Filias y fobias".
- Añadir **año** a cada pieza. Sin fecha, no se distingue lo nuevo de 2015.

## 9.5 P3 · Posicionamiento y canal propio *(continuo)*

12. **Título y metadatos con descriptor.** `MIKAN · Cantante y compositor de
    pop`, nunca "Mikan" a secas. El nombre colisiona con un cítrico japonés y con
    artistas homónimos: es la causa principal de que no sea encontrable.
13. **Datos estructurados** de tipo `MusicGroup` y `Person`, con enlaces a sus
    perfiles. Es lo que lee Google para construir la ficha de conocimiento.
14. **Alta en Wikidata** con referencias de prensa verificables.
15. **Formulario de newsletter** en portada, en el pie y en la sección de fechas.
16. **Sustituir los superlativos por pruebas enlazadas.** La nota de prensa de
    portada afirma cosas que el lector no puede verificar ("la actuación más
    reproducida de RTVE"). Como el vídeo existe y es bueno, **conviene enseñarlo
    en lugar de calificarlo**: el dato verificable convence a un profesional; el
    adjetivo lo pone en guardia.
17. **Peso y velocidad.** Una web de artista con 40 imágenes debe servirlas en
    formatos modernos y con carga diferida.

## 9.6 Orden de ejecución

| Paso | Qué | Tiempo |
|---|---|---|
| 1 | Arreglar los dos `mailto:` | 5 min |
| 2 | Corregir Apple, Spotify, Tienda y Contacto | 15 min |
| 3 | Subir la gala de RTVE a portada y a Videografía | 30 min |
| 4 | Crear "Prensa y contratación" con PDF, fotos en alta y rider | 2-3 h |
| 5 | Crear sección de fechas con captación de correo | 1 h |
| 6 | Reordenar Videografía, quitar duplicado, añadir años | 1 h |
| 7 | Títulos, metadatos y datos estructurados | 2 h |
| 8 | Wikidata | 1-2 h |

Los pasos 1 a 3 son **45 minutos** y son los que mueven la aguja. El resto puede
esperar a la semana siguiente sin coste.

## 9.7 Nota sobre el acceso a la web

El artista ha ofrecido acceso al gestor del sitio. Dos consideraciones:

1. **No conviene compartir usuarios ni contraseñas por chat ni por correo**, ni
   conmigo ni con nadie. Si en algún momento hace falta acceso real, se crea un
   usuario propio con permisos limitados y se revoca al terminar.
2. **Para esta fase no es necesario.** Todo lo de P0 y P1 son cambios puntuales
   y verificables desde fuera: los textos y los enlaces exactos están en este
   documento, listos para aplicar, y el resultado se puede comprobar
   públicamente una vez hechos.

El sitio presenta indicios de estar construido sobre WordPress (estructura de
URLs, imágenes `-scaled`, gestor de consentimiento de cookies). Si se quiere
trabajar sobre plantillas o contenido de forma más profunda, la vía ordenada es
volcar una copia o una exportación del contenido en este repositorio y trabajar
sobre ella, en lugar de operar en producción.
