---
title: "MIKAN · Arquitectura del perfil de Instagram"
author: "David Vallejo Pérez"
email: "david.vallejoperez@emeal.nttdata.com"
date: "2026-10-11"
version: "1.0"
---

# 17. Arquitectura del perfil de Instagram

## 17.1 Lo que no se puede hacer

**Instagram no permite reordenar la cuadrícula.** Las publicaciones aparecen en
orden cronológico de publicación y no existe función de reordenación, ni para
el titular de la cuenta ni para nadie. Las únicas formas de alterar lo que se ve
son:

| Acción | Efecto | Coste |
|---|---|---|
| **Fijar publicación** | Sube hasta **3** al principio del perfil | Ninguno. Es la palanca real |
| **Archivar** | La saca de la cuadrícula, reversible | Pierde visibilidad, conserva datos |
| **Borrar y volver a subir** | Cambia la posición | **Pierde fecha, comentarios e interacción.** Casi nunca compensa |

Y un aviso sobre el acceso: Instagram bloquea cuentas ante inicios de sesión
automatizados desde servidores desconocidos. Para un artista, perder la cuenta
es el daño más caro posible. **No se comparten credenciales de Instagram, con
nadie y por ningún motivo.**

## 17.2 Las tres publicaciones fijadas

Son las tres primeras tarjetas que ve quien llega al perfil. Deben responder a
«quién es», «qué vende» y «por qué fiarse», en ese orden.

| Posición | Qué fijar | Qué comunica |
|---|---|---|
| **1** | **Nebulossa en el Orgullo del Río de la Pila** | Credencial verificable, circuito LGTBIQ+ y una historia con emoción. Es la que más va a retener |
| **2** | **El show con público y la compañía al completo** | Qué se compra: catorce personas en escena y gente delante |
| **3** | **Gala de Nochevieja de RTVE La 1** | Prime time en televisión pública |

Si alguna de las tres no existe aún como publicación propia, **se publica**: el
material está en `web/fotos-directo/` y en los enlaces del inventario de
credenciales.

## 17.3 Los destacados: la estructura que sí controlas

Aquí está el verdadero equivalente a «ordenar el perfil», y hoy está
desaprovechado. Los destacados son permanentes, se ordenan a voluntad y son lo
primero que mira alguien que valora contratar.

Arquitectura recomendada, de izquierda a derecha:

| Destacado | Contenido |
|---|---|
| **▶ EL SHOW** | Directo, compañía de baile, público. El escaparate comercial |
| **★ TV** | Gala de RTVE, Los40, platós, Benidorm Fest |
| **🏳️‍🌈 ORGULLOS** | Río de la Pila, Nebulossa, circuito LGTBIQ+ |
| **📰 PRENSA** | Recortes, entrevistas, el premio de 2025 |
| **🎨 PROCESO** | Diseño de portadas, vestuario, estudio. Su rasgo diferencial |
| **📩 CONTRATACIÓN** | Formatos, qué incluye el show y el enlace a mikan.es/contratacion |

**Las portadas las diseña él**, que es ilustrador: seis portadas coherentes
convierten un perfil normal en uno que parece tener equipo detrás. Es media
tarde de trabajo y es el cambio visual de mayor impacto por hora invertida.

## 17.4 La biografía

Dos campos distintos y conviene no confundirlos:

- **Nombre** (el que Instagram indexa en su buscador): `MIKAN · Cantante`.
  Nunca «MIKAN» a secas: el nombre compite con un cítrico japonés y con
  artistas homónimos, y este campo es de los pocos que ayudan a encontrarle.
- **Biografía**:

```
THE MIKAN SHOW · Pop glam en directo
Gala Nochevieja RTVE La 1 · Benidorm Fest
Premio Mejor Canción 2025
📩 Contratación 2027 ↓
```

Y el **enlace** debe ir a `mikan.es/contratacion`, no a la portada de la web.
Quien pulsa el enlace de la bio de un artista con un show montado suele querer
una de dos cosas: escuchar o contratar. La página de contratación resuelve las
dos, porque lleva los vídeos dentro.

**Cuenta profesional**: si no lo es ya, conviene cambiarla. Da estadísticas
reales —que hoy no tenemos— y añade el botón de contacto directo.

## 17.5 Lo que de verdad ordena el perfil

Una advertencia de criterio, porque es donde se pierde más tiempo en este
oficio:

> **Reordenar el pasado no cambia nada. Lo que ordena un perfil es lo que se
> publica a partir de mañana.**

Tres publicaciones fijadas y seis destacados bien hechos resuelven la primera
impresión en una tarde. A partir de ahí, lo que construye el perfil es la
cadencia de `docs/06-contenido-y-redes.md`: cuatro piezas semanales producidas
en un solo bloque de grabación.

Y conviene recordar el orden de magnitud: **Instagram no contrata.** Es el sitio
donde un promotor te comprueba después de recibir tu correo, no donde te
descubre. Un perfil impecable sin propuestas enviadas no produce ni una fecha;
un perfil normal con 120 propuestas enviadas, sí.
