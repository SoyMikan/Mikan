---
title: "MIKAN · Vigilancia del buzón de contratación y seguridad de la cuenta de Instagram"
author: "David Vallejo Pérez"
email: "david.vallejoperez@emeal.nttdata.com"
date: "2026-10-14"
version: "1.0"
---

# 19. Vigilancia del buzón de contratación y seguridad de la cuenta de Instagram

Procedimiento para que los avisos de seguridad de Instagram que llegan a
`contratacion@mikan.es` **se detecten solos y salten al móvil**, sin construir
nada que pulse enlaces por su cuenta. La automatización que sí conviene es la
que **avisa**, no la que **actúa**: un filtro etiqueta y marca el correo, y el
móvil lo notifica; la decisión de qué hacer sigue siendo de una persona, que es
el único punto donde se distingue un aviso real de una imitación.

---

## 19.1 Por qué no se automatiza el clic

El correo que motivó esto es una **alerta de seguridad de Instagram** («detectamos
un inicio de sesión desde un dispositivo que no usas habitualmente»). El enlace
`instagram.com/profile_change_disavow?...&token=...` y el botón **«No he sido
yo»** sirven para rechazar ese acceso.

Automatizar que ese botón se pulse solo, siempre, sin que nadie mire, es un mal
movimiento por cuatro razones concretas:

1. **Un bot que pulsa enlaces con token de correos «de seguridad» es,
   estructuralmente, lo que hace el phishing.** La automatización no distingue un
   correo real de Instagram de una imitación convincente: el día que llegue uno
   falso con un botón «Asegura tu cuenta» que apunte a otro sitio, lo pulsará
   igual que los legítimos.
2. **Anula el único valor de la alerta**, que es el criterio humano: «¿espero yo
   este inicio de sesión ahora mismo?».
3. **Se puede convertir en arma**: si alguien puede provocar estos correos
   reintentando accesos, una automatización que reacciona siempre igual sobre
   acciones de cuenta es superficie de ataque, no protección.
4. **Instagram no tiene API para este flujo.** La única forma de automatizarlo
   sería un navegador headless que entrara al correo y pulsara el enlace —
   exactamente el acceso automatizado desde servidor desconocido que, como
   recoge `docs/17-instagram.md` §17.1, hace que Instagram **bloquee la
   cuenta**, que es el daño más caro posible para un artista.

Por eso lo que se monta aquí es **detección + aviso**. El clic lo da una persona,
después de comprobar.

---

## 19.2 Arquitectura elegida (móvil Android)

El móvil es **Android**, y en Android el push fiable lo da la **app de Gmail**,
que notifica **por etiqueta** y con sonido propio —cosa que la app del webmail
no hace bien—. El correo de `contratacion@mikan.es` vive en el webmail del
dominio pero ya se recibe también en Gmail, así que no hay que mover la cuenta a
ningún sitio. El diseño, lo más limpio posible, son **dos piezas**:

| Pieza | Dónde | Papel |
|---|---|---|
| **Filtro** | Gmail | Detecta el aviso, lo etiqueta, lo marca importante y lo destaca |
| **Notificación push** | App de Gmail en Android | Avisa en el momento, con sonido propio por etiqueta |

Y **una tercera pieza solo si hace falta**: si Gmail recoge el correo del webmail
por **POP** (consulta periódica), estos avisos pueden tardar de 15 min a 1 h en
aparecer, lo que para una alerta de seguridad es demasiado. En ese caso —y solo
en ese caso— se añade una regla de **reenvío** en el webmail que empuje estos
avisos a Gmail al instante (§19.6).

> **El principio que lo mantiene limpio:** el webmail **reenvía**, nunca se usa
> para «mover» el correo. Un reenvío ocurre en la entrega, antes de que Gmail
> recoja nada, así que no hay forma de que el mensaje «desaparezca» antes del
> push. Si en vez de reenviar se moviera el correo a una carpeta del webmail,
> una recogida por POP podría no verlo nunca. Por eso: reenviar sí, mover no.

**Cómo saber si tienes retardo (POP) o entrega instantánea:** en Gmail
(ordenador) → **Ajustes → Cuentas e importación → «Consultar el correo de otras
cuentas»**. Si ahí aparece `contratacion@mikan.es`, es **POP** y conviene añadir
la regla de §19.6. Si **no** aparece —porque el webmail ya reenvía todo a
Gmail—, la entrega es instantánea y con las dos piezas basta.

---

## 19.3 El filtro en Gmail

### Paso 1 · Crear la etiqueta

En Gmail (ordenador): rueda dentada → **Ver todos los ajustes** → pestaña
**Etiquetas** → **Crear etiqueta nueva**:

```
⚠ Seguridad-Instagram
```

El emoji no es decorativo: hace que el aviso salte a la vista entre cientos de
correos de booking.

### Paso 2 · Crear el filtro

Barra de búsqueda de Gmail → icono de controles (los deslizadores a la derecha)
→ en **«Incluye las palabras»** pegar **exactamente** esta cadena, y dejar el
resto de campos en blanco:

```
from:(instagram.com OR facebookmail.com) to:(contratacion@mikan.es) ("profile_change_disavow" OR "secure your account" OR "asegura tu cuenta" OR "nuevo inicio de sesión" OR "new login" OR "inicio de sesión" OR "no he sido yo" OR "wasn't you")
```

Qué hace cada parte:

- `from:(instagram.com OR facebookmail.com)` — los avisos de seguridad de
  Instagram salen de `security@mail.instagram.com` y, en cuentas antiguas, de
  `@facebookmail.com`. Ambos dominios contienen esas cadenas, así que los dos
  casan. **No** se abre a cualquier remitente: un correo que imite el texto pero
  venga de otro dominio **no** entra en el filtro, que es justo lo que se busca.
- `to:(contratacion@mikan.es)` — acota a este buzón y no a otras cuentas que
  puedan estar en el mismo Gmail.
- El bloque entre paréntesis — las frases típicas de estos avisos, en inglés y
  español, incluido el literal del enlace y del botón del correo original.

Pulsar **Crear filtro** (abajo a la derecha del panel).

### Paso 3 · Las acciones del filtro

En la segunda pantalla, marcar:

- [x] **Destacar** *(Star)*
- [x] **Aplicar la etiqueta:** `⚠ Seguridad-Instagram`
- [x] **Marcar siempre como importante**
- [x] **Aplicar también a las conversaciones que cumplan los criterios** — para
      capturar los avisos que ya estén en el buzón.
- [ ] **No enviar nunca a Spam** — *dejar SIN marcar por defecto.* El filtro
      antispam de Gmail es una defensa útil contra imitaciones; desactivarlo para
      «cualquier cosa que diga venir de Instagram» abriría la puerta justo a lo
      que queremos evitar. Marcarlo **solo** si se comprueba que algún aviso
      legítimo acaba en Spam, y aun así nunca para un remitente que no sea el
      dominio real.

Pulsar **Crear filtro**.

---

## 19.4 El push al móvil (Android)

Esta es la pieza que convierte el filtro en un aviso que suena. En la **app de
Gmail** del Android:

1. App de Gmail → menú ☰ → **Ajustes** → tocar la cuenta `contratacion@mikan.es`.
2. **Notificaciones** → activarlas (**Todas** o **Alta prioridad**; con el
   filtro marcando estos avisos como importantes, «Alta prioridad» basta y deja
   fuera el ruido de booking).
3. Volver a **Ajustes** → **Administrar etiquetas** → `⚠ Seguridad-Instagram`:
   - **Sincronizar mensajes:** activado *(sin esto la etiqueta no notifica)*.
   - **Notificaciones de etiqueta:** activado.
   - Elegir un **sonido distinto** del general. Que el aviso suene distinto a un
     correo de booking cualquiera es la mitad del valor de todo esto: lo
     reconoces sin mirar la pantalla.

Resultado: solo estos avisos disparan ese sonido, en el momento. El resto del
correo de contratación notifica como siempre, o no notifica, según lo tengas.

---

## 19.5 Qué hacer cuando salte el aviso

Un aviso sin un protocolo de respuesta es solo ruido nuevo. Cuando suene:

1. **No pulsar el enlace del correo.** Nunca, aunque parezca legítimo.
2. **Abrir Instagram por vía propia** — la app, o escribiendo `instagram.com` a
   mano en el navegador— y entrar en **Configuración → Centro de cuentas →
   Contraseña y seguridad → Actividad de inicio de sesión** («Where you're
   logged in»). Ahí se ven las sesiones abiertas y se cierra cualquiera que no
   se reconozca, **sin pasar por ningún enlace de correo**.
3. **Si aparece un acceso que no eres tú:** cerrar esa sesión, **cambiar la
   contraseña** desde esa misma pantalla y revisar que el correo y el teléfono
   de recuperación siguen siendo los correctos.
4. **Si no reconoces nada y el correo es sospechoso:** es phishing. Marcarlo y
   borrarlo. No pulsar nada.

---

## 19.6 La regla del webmail (solo si hay retardo por POP)

Si la comprobación de §19.2 dio que Gmail recoge por **POP**, estos avisos
llegarían con retardo. Esta regla lo arregla reenviándolos a Gmail **al
instante**, sin esperar a la siguiente recogida. Si la entrega ya era
instantánea (el webmail reenvía todo a Gmail), **este paso no hace falta**.

En el webmail del dominio → **Ajustes → Filtros / Reglas → Nueva regla**:

- **Si** `De` contiene `instagram.com` **y** (`Asunto` **o** `Cuerpo`) contiene
  `inicio de sesión` **o** `login` **o** `secure your account`,
- **Entonces: Reenviar a** la dirección de Gmail donde ya recibes el correo.
  - **Dejar también una copia en la bandeja del webmail** (casi todos los
    webmails ofrecen «reenviar **y** conservar copia»; si da a elegir, conservar
    copia).
  - **No** marcar «mover a carpeta» ni «borrar»: reenviar sí, mover no (§19.2).

Con esto el aviso entra en Gmail en segundos, el filtro de §19.3 lo etiqueta y
la app de Android lo notifica. El webmail queda como punto de captura en el
origen —que se dispara con todos los mensajes, no dependa de Gmail— y Gmail como
motor de push.

> Esta regla reenvía **solo los avisos de seguridad de Instagram**, no todo el
> correo de contratación. Es deliberado: no cambia cómo recibes el resto, solo
> acelera la clase de mensaje que no puede esperar una hora.

---

## 19.7 La corrección de fondo (lo que de verdad apaga estos avisos)

El filtro resuelve el *enterarse*. Pero que estos avisos lleguen de forma
repetida —y para un usuario, `charo.hergar`— significa una de dos: **alguien
intenta entrar en la cuenta**, o **son correos de phishing**. En ambos casos la
respuesta de fondo es de seguridad, no de automatización:

- **Contraseña única y larga** para Instagram, no reutilizada en ningún otro
  sitio.
- **Verificación en dos pasos (2FA) con app de autenticación** (Google
  Authenticator, Authy…), **no por SMS**. Con 2FA bien puesto, un inicio de
  sesión desde un dispositivo nuevo no prospera aunque alguien tenga la
  contraseña, y los avisos **cesan en origen**.
- **Guardar los códigos de recuperación** de Instagram en lugar seguro.
- **Coherente con `docs/17-instagram.md` §17.1:** no se comparten las
  credenciales de Instagram con nadie y por ningún motivo. Este filtro no
  necesita acceso a la cuenta: trabaja solo sobre el correo.

Hecho esto, el filtro pasa de ser el guardia principal a ser lo que debe ser:
una red de seguridad para el caso raro.

---

## 19.8 Comprobación

Para verificar que todo funciona sin esperar a un aviso real:

1. Desde otra dirección, enviar a `contratacion@mikan.es` un correo de prueba
   cuyo texto incluya la frase `nuevo inicio de sesión` y la palabra
   `instagram.com`. *(No casará el `from:` real de Instagram, así que para la
   prueba conviene relajar temporalmente el filtro a solo el bloque de palabras,
   y restaurarlo después.)*
2. Confirmar que llega con la etiqueta `⚠ Seguridad-Instagram`, destacado y como
   importante.
3. Confirmar que el móvil suena con el aviso de etiqueta.
4. Restaurar el filtro a la cadena completa de §19.3.

Igual que con los canales de contacto (`docs/12` §12.5), conviene **reprobar el
aviso una vez al trimestre**: dos minutos, y es la diferencia entre que funcione
y creer que funciona.
