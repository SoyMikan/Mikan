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

## 19.2 Arquitectura elegida

El correo de `contratacion@mikan.es` vive en el **webmail del dominio**, pero se
recibe también en **Gmail**. El push al móvil lo da **Gmail**, que es quien
tiene app con notificación fiable por etiqueta. Por tanto:

| Pieza | Dónde | Papel |
|---|---|---|
| **Filtro principal** | Gmail | Detecta el aviso, lo etiqueta, lo marca importante y lo destaca |
| **Notificación push** | App de Gmail en el móvil | Avisa en el momento, con sonido propio |
| **Regla espejo** | Webmail del dominio | Red de seguridad: marca y archiva una copia. **Opcional y con una salvedad** (§19.6) |

> **Salvedad importante de orden.** Si Gmail recoge el correo del webmail por
> **POP**, una regla en el webmail que **mueva** el mensaje fuera de la bandeja
> de entrada **antes** de que Gmail lo recoja hará que Gmail —y el push— no lo
> vean nunca. Por eso el filtro principal va en Gmail, y la regla del webmail,
> si se pone, **solo marca o copia, nunca saca de la bandeja**. Detalle en §19.6.

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

## 19.4 El push al móvil

### Android (app de Gmail)

1. App de Gmail → menú ☰ → **Ajustes** → tocar la cuenta `contratacion@mikan.es`.
2. **Notificaciones** → activarlas (**Todas** o **Alta prioridad**).
3. Volver a **Ajustes** → **Administrar etiquetas** → `⚠ Seguridad-Instagram`:
   - **Sincronizar mensajes:** activado.
   - **Notificaciones de etiqueta:** activado.
   - Elegir un **sonido distinto** del general. Que el aviso suene distinto a un
     correo de booking cualquiera es la mitad del valor de todo esto.

Resultado: solo estos avisos disparan ese sonido, y lo hacen en el momento.

### iPhone (app de Gmail)

La app de Gmail en iOS **no** permite notificación por etiqueta; la
configuración es por cuenta. La vía equivalente:

1. App de Gmail → **Ajustes** → cuenta `contratacion@mikan.es` → **Notificaciones**
   → **Alta prioridad**.
2. Como el filtro ya marca estos avisos **como importantes**, entran en «alta
   prioridad» y notifican, mientras el resto del ruido no.

Si se quiere un aviso aún más separado en iPhone, la alternativa es añadir la
cuenta a la **app Mail de Apple** con una regla/VIP, pero para el objetivo
—enterarse en el momento— «Alta prioridad» en Gmail es suficiente.

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

## 19.6 La regla espejo en el webmail (opcional)

Solo si se quiere una copia marcada también en el webmail del dominio. **Con la
salvedad de §19.2:** no debe sacar el mensaje de la bandeja de entrada si Gmail
lo recoge de ahí por POP.

En el webmail → **Ajustes → Filtros / Reglas → Nueva regla**:

- **Si** `De` contiene `instagram.com` **y** (`Asunto` o `Cuerpo`) contiene
  `inicio de sesión` o `login` o `secure your account`,
- **Entonces:**
  - Marcar / señalar *(flag)* el mensaje. ✔
  - Opcional: copiar a una carpeta `Seguridad-Instagram`. Usar **«copiar a»**, no
    **«mover a»**, para que el original siga en la bandeja y Gmail lo recoja.
  - **No** activar reenvío automático ni borrado.

Si Gmail **no** recoge por POP sino que el webmail **reenvía** a Gmail, entonces
sí se puede mover a carpeta sin romper el push, porque el reenvío ocurre en la
entrega, antes de la regla. Ante la duda, dejar la regla del webmail solo en
«marcar» y confiar el push a Gmail.

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
