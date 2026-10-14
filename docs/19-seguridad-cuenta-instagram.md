---
title: "MIKAN · Vigilancia del buzón de contratación y seguridad de la cuenta de Instagram"
author: "David Vallejo Pérez"
email: "david.vallejoperez@emeal.nttdata.com"
date: "2026-10-14"
version: "2.0"
---

# 19. Vigilancia del buzón de contratación y seguridad de la cuenta de Instagram

Procedimiento para que los avisos de seguridad de Instagram que llegan a
`contratacion@mikan.es` **se detecten solos y suenen en el móvil**, sin construir
nada que pulse enlaces por su cuenta. La automatización que conviene es la que
**avisa**, no la que **actúa**: una regla detecta el aviso y lo lleva al teléfono;
la decisión de qué hacer sigue siendo de una persona, que es el único punto donde
se distingue un aviso real de una imitación.

---

## 19.1 Por qué no se automatiza el clic

El correo que motivó esto es una **alerta de seguridad de Instagram** («detectamos
un inicio de sesión desde un dispositivo que no usas habitualmente»). El enlace
`instagram.com/profile_change_disavow?...&token=...` y el botón **«No he sido
yo»** sirven para rechazar ese acceso.

Automatizar que ese botón se pulse solo, siempre, sin que nadie mire, es un mal
movimiento por cuatro razones:

1. **Un bot que pulsa enlaces con token de correos «de seguridad» es,
   estructuralmente, lo que hace el phishing.** La automatización no distingue un
   correo real de Instagram de una imitación convincente: el día que llegue uno
   falso con un botón «Asegura tu cuenta» que apunte a otro sitio, lo pulsará
   igual que los legítimos.
2. **Anula el único valor de la alerta**, que es el criterio humano: «¿espero yo
   este inicio de sesión ahora mismo?».
3. **Se puede convertir en arma**: si alguien provoca estos correos reintentando
   accesos, una automatización que reacciona siempre igual sobre acciones de
   cuenta es superficie de ataque, no protección.
4. **Instagram no tiene API para este flujo.** La única forma de automatizarlo
   sería un navegador headless que entrara al correo y pulsara el enlace —
   exactamente el acceso automatizado desde servidor desconocido que, como
   recoge `docs/17-instagram.md` §17.1, hace que Instagram **bloquee la
   cuenta**, el daño más caro posible para un artista.

Por eso lo que se monta es **detección + aviso**. El clic lo da una persona,
después de comprobar.

---

## 19.2 Cómo está montado (el camino real)

El dato de partida: `contratacion@mikan.es` **no es una cuenta de Gmail**. Vive
en el **webmail de IONOS** (se entra en `mail.ionos.es` con esa dirección y su
contraseña). Por tanto no se puede «entrar en Gmail» con ella.

La solución más limpia aprovecha lo que ya existe: un **Gmail personal**
(`angelfired@gmail.com`) que ya está instalado en el móvil Android y que **avisa
bien**. El montaje es **un solo cambio**:

> **En IONOS se crea una regla que, cuando llega un aviso de Instagram, lo
> REENVÍA a `angelfired@gmail.com`.** Como ese Gmail ya está en el móvil, el
> teléfono suena. No hay que mover la cuenta de mikan a ningún sitio.

| Pieza | Dónde | Papel |
|---|---|---|
| **Regla de reenvío** | Webmail de IONOS | Detecta el aviso y lo manda al Gmail personal, al instante |
| **Notificación** | App de Gmail en el Android | Suena al llegar el correo reenviado |
| **Sonido propio** *(opcional)* | Filtro + etiqueta en Gmail | Para que el aviso suene distinto del resto (§19.5) |

El principio que lo mantiene limpio: la regla **reenvía**, no **mueve**. El
original se conserva en el buzón de mikan y una copia va al Gmail. Y reenvía
**solo** los avisos de Instagram, no todo el correo de contratación.

---

## 19.3 La regla en IONOS (el paso principal)

### 1 · Entrar en el webmail de IONOS

En el navegador, ir a **`mail.ionos.es`** e iniciar sesión con:

- Usuario: **`contratacion@mikan.es`**
- Contraseña: la de ese correo.

### 2 · Abrir las reglas de filtro

- Arriba a la derecha, el **engranaje ⚙ (Ajustes)**.
- En el menú de la izquierda: **Correo → Reglas de filtro**.
- Pulsar **Añadir nueva regla**.

### 3 · Configurar la regla

- **Nombre:** `Aviso seguridad Instagram`
- **Añadir condición** → **Remitente (De)** → **Contiene** → `instagram.com`
- **Añadir acción** → **Redirigir a** → `angelfired@gmail.com`
- **Añadir acción** → **Conservar** *(mantiene también la copia en el buzón de
  mikan; si no se añade, algunos webmails dejan de guardar el original)*
- **Guardar**.

Con esto, cada aviso de Instagram que entre en `contratacion@mikan.es` llega en
segundos a `angelfired@gmail.com` y el móvil avisa.

> **Si IONOS no muestra «Reglas de filtro»** en esa versión del webmail, la
> alternativa es el panel de control (`my.ionos.es` → **Correo** → la dirección →
> **Reenvío**). Pero ese reenvío manda **todo** el correo, no solo los avisos;
> es menos limpio y solo se usa si la regla de filtro no está disponible.

---

## 19.4 El aviso en el móvil (Android)

De serie no hay que tocar nada: como `angelfired@gmail.com` ya está en la app de
Gmail del Android, el correo reenviado notifica como cualquier otro.

Para que **suene distinto** del resto y se reconozca sin mirar la pantalla —muy
recomendable—, ver §19.5.

---

## 19.5 (Opcional) Que el aviso suene distinto en Gmail

En el ordenador, en **gmail.com** con la cuenta `angelfired@gmail.com`:

1. Rueda dentada → **Ver todos los ajustes** → pestaña **Etiquetas** → **Crear
   etiqueta nueva**: `⚠ Seguridad-Instagram`.
2. En la barra de búsqueda de Gmail, pegar y buscar:
   ```
   from:(instagram.com)
   ```
3. Icono de **controles deslizantes** (a la derecha de la barra) → **Crear
   filtro** → marcar:
   - ☑ **Destacar**
   - ☑ **Marcar siempre como importante**
   - ☑ **Aplicar la etiqueta** `⚠ Seguridad-Instagram`
   - ☑ **Aplicar también a las conversaciones que cumplan los criterios**
   - **Crear filtro**.

En la **app de Gmail** del Android:

- ☰ → **Ajustes** → cuenta `angelfired@gmail.com` → **Administrar etiquetas** →
  `⚠ Seguridad-Instagram` → **Sincronizar: Sí**, **Notificaciones de etiqueta:
  activado**, y elegir un **sonido distinto**.

Resultado: solo estos avisos suenan con ese tono.

---

## 19.6 Qué hacer cuando salte el aviso

Un aviso sin protocolo de respuesta es solo ruido nuevo. Cuando suene:

1. **No pulsar el enlace del correo.** Nunca, aunque parezca legítimo.
2. **Abrir Instagram por vía propia** —la app, o escribiendo `instagram.com` a
   mano— y entrar en **Configuración → Centro de cuentas → Contraseña y
   seguridad → Actividad de inicio de sesión**. Ahí se ven las sesiones abiertas
   y se cierra cualquiera que no se reconozca, **sin pasar por el correo**.
3. **Si aparece un acceso que no eres tú:** cerrar esa sesión, **cambiar la
   contraseña** y revisar el correo y el teléfono de recuperación.
4. **Si no reconoces nada y el correo es sospechoso:** es phishing. Marcarlo y
   borrarlo. No pulsar nada.

---

## 19.7 La corrección de fondo (lo que apaga estos avisos)

Que estos avisos lleguen de forma repetida —y para un usuario, `charo.hergar`—
significa una de dos: **alguien intenta entrar en la cuenta**, o **son correos de
phishing**. En ambos casos la respuesta de fondo es de seguridad:

- **Contraseña única y larga** para Instagram, no reutilizada en ningún otro
  sitio.
- **Verificación en dos pasos (2FA) con app de autenticación** (Google
  Authenticator, Authy…), **no por SMS**. Con 2FA, un inicio de sesión desde un
  dispositivo nuevo no prospera aunque tengan la contraseña, y los avisos
  **cesan en origen**.
- **Guardar los códigos de recuperación** de Instagram en lugar seguro.
- **Coherente con `docs/17-instagram.md` §17.1:** no se comparten las
  credenciales de Instagram con nadie. Esta regla no necesita acceso a la cuenta:
  trabaja solo sobre el correo.

Hecho esto, la regla de reenvío pasa de ser el guardia principal a ser lo que
debe ser: una red de seguridad para el caso raro.

---

## 19.8 Comprobación

1. Cuando llegue el próximo aviso de Instagram, confirmar que aparece en
   `angelfired@gmail.com` y que el móvil suena.
2. **Si acaba en Spam** en Gmail (puede pasar con correo reenviado), crear en
   Gmail un filtro `from:(instagram.com)` con la acción **«No enviar nunca a
   Spam»**, para que el aviso no se pierda.

Igual que con los canales de contacto (`docs/12` §12.5), conviene **reprobar el
aviso una vez al trimestre**: dos minutos, y es la diferencia entre que funcione
y creer que funciona.
