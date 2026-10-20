# Coorporacion cordova · Sistema de ventas y administración

Dos experiencias independientes sobre **una sola base de datos**:

| | Acceso | Para qué sirve |
|---|---|---|
| **Empleados** | `/empleados/login` | Punto de venta: catálogo con fotos, kilos exactos, cliente, carrito, comprobante e historial. Nada más. |
| **Administración** | `/admin/login` | Controla la empresa: empleados y permisos, productos, precios, fotos, inventario, ventas, clientes, compras, finanzas, reportes, auditoría y **configuración/personalización sin tocar código**. |

> El nombre interno del código sigue siendo **SIGA** (carpeta de base de datos, clave del tema). Lo visible lleva la marca «Coorporacion cordova».
> **Punto de partida:** el sistema arranca con el **catálogo y el inventario** (20 cortes de cerdo con sus precios por kg, categorías, stock, proveedores y configuración) y **sin ventas, compras, clientes ni finanzas de ejemplo**. El personal es solo este:
>
> | Persona | Usuario | Rol |
> |---|---|---|
> | Tonen Bermudez Castro | `tonen.bermudez` | Administrador |
> | Eda Luz Cordova Eusebio | `eda.cordova` | Administrador |
> | Brandon Aquim Bermudez Cordova | `brandon.bermudez` | Vendedor |
>
> Sus fichas tienen **DNI provisional (`00000001`…`00000003`) y sueldo en 0**: complétalos en *Gestión de empleados*. Los precios, el stock y los proveedores del catálogo son los iniciales y el negocio debe confirmarlos o ajustarlos. El nombre legal y el RUC no se inventaron: se completan en *Configuración*.
---

## 1. Ejecutarlo en Windows (paso a paso)

1. Instala **Node.js 22 LTS** (o superior; mínimo 22.13) desde <https://nodejs.org>. Acepta las opciones por defecto.
2. Descomprime el ZIP, por ejemplo en `C:\coorporacion-cordova`.
3. Abre esa carpeta, haz clic en la barra de direcciones, escribe `powershell` y pulsa Enter.
4. Ejecuta:
   ```powershell
   npm start
   ```
   (No hace falta `npm install`: el sistema no usa paquetes externos.)
5. **Mira la consola.** En el primer arranque se crea una cuenta para cada persona, con contraseña **aleatoria** que se muestra **una sola vez**:
   ```
   ADMINISTRADOR Tonen Bermudez Castro        usuario: tonen.bermudez    contraseña: xxxxxxxxxxxx
   ADMINISTRADOR Eda Luz Cordova Eusebio      usuario: eda.cordova       contraseña: yyyyyyyyyyyy
   EMPLEADO      Brandon Aquim Bermudez ...   usuario: brandon.bermudez  contraseña: zzzzzzzzzzzz
   ```
   Entrega a cada quien la suya. Al entrar por primera vez el sistema le pedirá elegir una contraseña nueva.
6. Abre en el navegador:
   - Empleados: <http://localhost:3000/empleados/login>
   - Administración: <http://localhost:3000/admin/login>
7. Para detenerlo: `Ctrl + C`. Para usarlo desde un celular en la misma red Wi-Fi: `http://IP-de-la-PC:3000` (Windows pedirá permitir el acceso en el firewall).

**¿Perdiste una contraseña?** `npm run clave -- admin` genera una temporal nueva (hay que ejecutarlo en el equipo del servidor, por eso es seguro).
**¿Quieres empezar de cero?** `npm run reset` borra la base **y las imágenes subidas** (la siguiente vez que inicies vuelve a quedar el catálogo de partida y el personal de arriba).

### Dejar tu base actual así (sin perder el catálogo)
Si ya venías usando el sistema y quieres quedarte **solo con el catálogo y el inventario** y con el personal de arriba:
1. Detén el servidor (`Ctrl + C`).
2. Ejecuta `npm run limpiar` y escribe `SI` cuando lo pida.

**Se conserva:** productos, precios y su historial, categorías, stock, fotos subidas, proveedores y la configuración (datos de la empresa, colores, logotipo).
**Se borra:** ventas, compras, movimientos de finanzas, clientes (queda «Consumidor final»), empleados, cuentas, auditoría y accesos. El kardex vuelve a empezar con el stock actual como «Saldo inicial».
Antes de borrar guarda una **copia de seguridad** en `data\respaldo-antes-de-limpiar-….db` y al final muestra las contraseñas temporales de las tres cuentas.
**Copia de seguridad:** copia la carpeta `data\` (contiene `siga.db` y `uploads\`). Para restaurar, ponla de nuevo en su lugar con el servidor detenido.

### Si ya tenías la versión anterior
Al iniciar, la base se **migra sola** (v1 → v2) conservando ventas, productos, clientes y finanzas; el stock queda con «Saldo inicial» en el kardex. Se probó con una base real de la versión anterior (190 ventas, mismo stock e importes). Las categorías antiguas se mantienen y puedes renombrarlas en *Catálogo de carnes*. Se recomienda copiar `data\` antes de actualizar.

---

## 2. Manual rápido

### Empleado (aprende a vender en minutos)
1. Entra con su usuario. Ve el catálogo con fotos, **precio por kg** y disponibilidad. Busca con `/` o filtra por categoría.
2. **Agregar a venta** → escribe los kilos (`5.5`, `0.250`, `100`; botones rápidos; admite toneladas). Ve el importe al instante.
3. Elige el **cliente** (buscador, «Consumidor final» o *Registrar cliente nuevo*), el **método de pago** y, si su cuenta lo permite, un descuento.
4. **Confirmar venta** → el servidor revalida precios y stock; si algo cambió, se lo muestra. Luego revisa y registra. Obtiene el número `V001-xxxx` y puede **imprimir el comprobante** (o guardarlo como PDF desde el diálogo de impresión).
5. **Historial**: sus ventas (o las de todos, si el administrador se lo permitió).

### Administrador
- **Cambiar un precio / foto:** *Gestión de productos* → *Actualizar precio* o *Cambiar imagen* (desde la computadora o el celular). Los empleados lo ven de inmediato; las ventas anteriores no cambian.
- **Crear un empleado:** *Gestión de empleados* → *Agregar empleado*: datos, usuario, rol y permisos. Se genera una contraseña temporal que se muestra una vez.
- **Inventario:** *Ajustar stock* y *Merma* piden motivo y quedan en el **kardex**. Las entradas se registran como *Compras*.
- **Apariencia y datos:** *Configuración empresarial* → datos de la empresa, IGV y métodos de pago, **Personalización** (colores, logotipo, imágenes de los accesos, banner, textos).
- **Proveedores (eliminación libre):** *Proveedores → Eliminar* funciona con cualquier proveedor, tenga o no productos o compras. La ventana te dice cuántos tiene y te deja elegir **a quién pasan sus productos**: por defecto a «Sin proveedor asignado» (opción segura), o a otro proveedor tuyo. Las compras ya registradas se conservan en el historial a nombre de «Sin proveedor asignado» (no se atribuyen a otro proveedor, para no falsear quién suministró). Lo eliminado queda detallado en *Auditoría*. Para empezar con los tuyos: agrega tus proveedores, elimina los de ejemplo pasando sus productos a los tuyos (o dejándolos «sin proveedor» y asignándolos luego desde *Gestión de productos → Editar*). Un producto sin proveedor se puede vender, pero **no comprar** hasta asignarle uno.
- **Auditoría:** quién cambió qué y cuándo (precios, anulaciones, inventario, empleados, configuración) y los accesos al sistema.

### Entrar con Gmail (correo y «Continuar con Google»)
Cada cuenta puede tener un **correo de Gmail** registrado en la ficha del empleado (*Gestión de empleados → Editar → Acceso al sistema*). Con él:
- **El usuario se genera solo** a partir del correo (`ana.perez@gmail.com` → `ana.perez`; si ya existe, se agrega un número).
- La persona puede **entrar escribiendo el correo en lugar del usuario**, con su contraseña.
- Si activas Google, puede **entrar con el botón «Continuar con Google»**, sin contraseña.
- La **contraseña sigue siendo aleatoria**: nunca se deriva del correo (sería adivinable). Gmail ignora los puntos y el `+etiqueta`, y el sistema también (`ana.perez+x@gmail.com` = `anaperez@gmail.com`).

**Activar el botón de Google (una sola vez, ~10 minutos):** *Configuración empresarial → Acceso con Google* explica los pasos con la dirección exacta que debes registrar en Google Cloud (`https://…/auth/google/callback`). Pegas el *ID de cliente* y el *secreto*; el secreto se guarda **cifrado** y no se vuelve a mostrar. También puede configurarse con variables de entorno (`GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `PUBLIC_URL`).

**Reglas de seguridad:**
- Google solo confirma **quién** es la persona. Entra únicamente quien tenga su correo **registrado en una ficha activa**; el rol y los permisos los decide el sistema. Un empleado **no puede** entrar a administración con Google, ni al revés.
- Se usa el flujo estándar de código de autorización con **PKCE**, `state` anti-CSRF, y se verifican el `nonce`, la audiencia, el emisor, la caducidad y que Google haya **verificado** el correo.
- Quien entra con Google por primera vez teniendo una clave temporal **la deja anulada** (para no dejar una contraseña conocida y activa). Si necesita acceso con contraseña, el administrador usa *Restablecer clave*.
- La seguridad de este acceso depende de la cuenta de Google de cada persona: conviene que active la verificación en dos pasos.
- Los intentos fallidos con Google quedan en *Auditoría → Accesos*.

**Importante:** el flujo se probó completo contra un **Google simulado** (que exige secreto, PKCE y dirección de retorno exactos). La conexión con el **Google real** solo puede confirmarse con tus credenciales: pruébala una vez al configurarla. Mientras la aplicación esté en modo «Prueba» en Google, solo entran las cuentas que agregues como *usuarios de prueba*. Fuera de tu equipo, Google exige **https**.

---

## 3. Seguridad (lo que realmente hace el servidor)

- Contraseñas con **scrypt** y sal propia; **nunca** se guardan ni se muestran en texto plano. Mínimo 10 caracteres con letras y números. Las claves temporales obligan a cambiarlas.
- **Sesiones** en base de datos (solo se guarda el hash del token), cookie `HttpOnly` + `SameSite=Strict` (+ `Secure` bajo HTTPS), caducidad por inactividad (2 h) y total (10 h). Una cookie por área: la de empleado **no sirve** en administración.
- **Permisos validados en cada llamada** (`/api/admin/*` solo administradores, `/api/pos/*` solo empleados). Cambiar la URL a mano no sirve: el servidor redirige o responde 401/403. El empleado no tiene rutas para cambiar precios ni roles.
- Nadie se asigna rol: solo un administrador, desde su panel. **No hay registro público.** Siempre queda al menos un administrador activo.
- **Bloqueo por intentos:** 5 fallos en 15 minutos bloquean al usuario (aun con la clave correcta); también hay límite por IP. Mensaje de error idéntico para «usuario inexistente» y «clave incorrecta».
- **Recuperación de acceso:** otro administrador restablece la clave; el único administrador usa `npm run clave -- usuario` en el servidor. No se envían contraseñas por correo.
- Protección **CSRF** (cabecera propia + comprobación de origen + SameSite), cabeceras de seguridad (CSP, X-Frame-Options, nosniff, HSTS en HTTPS), consultas parametrizadas, escape de salida.
- **Imágenes:** se valida el *contenido* real (PNG/JPG/WebP), máximo 4 MB, nombre aleatorio, sin ejecución. Se corrigió y se probó un intento de salir de la carpeta pública (`..%2f`).
- **Ventas:** precios, importes, IGV y stock los calcula el servidor con enteros (gramos × céntimos); transacción única (si falla algo no queda media venta); clave de operación contra doble clic; revalidación al confirmar.
- Todo cambio administrativo queda en **auditoría**.

**Límites conocidos:** no hay verificación en dos pasos ni recuperación por correo; el bloqueo por usuario podría usarse para molestar a alguien (el administrador puede restablecerlo con `npm run clave`); SQLite en un solo servidor.

---

## 4. Publicar en internet (Railway u otro)

El código está **preparado**, pero **no se desplegó ni se verificó en una URL real** (no se puede desde aquí). Pasos:

1. Sube la carpeta a un repositorio de GitHub (el archivo `.gitignore` ya excluye `data/` y `.env`).
2. En Railway: *New Project → Deploy from GitHub*. Usa `railway.json` (comando `npm start`, chequeo de salud `/api/salud`).
3. Añade un **Volumen** y móntalo en `/data`. **Sin volumen, los datos y las fotos se pierden en cada despliegue.**
4. En *Variables* define (ver `.env.example`): `DATA_DIR=/data`, `TRUST_PROXY=1`, `SIN_DATOS=1` (empezar con base vacía), `ADMIN_PASSWORD` (clave inicial de Tonen; si no, léela en los *logs* del primer arranque, donde salen las tres cuentas). En producción con base vacía usa `SIN_DATOS=1`; con `SIN_DATOS=0` nace con el catálogo de partida.
5. Genera el dominio (HTTPS lo pone Railway). Tus direcciones serán `https://tu-dominio/empleados/login` y `https://tu-dominio/admin/login`.
6. **Antes de darlo por terminado:** abre ambas URLs desde otra red (por ejemplo, datos móviles), inicia sesión, registra una venta y haz un nuevo despliegue para confirmar que los datos siguen ahí.

**Variables:** `PORT` · `DATA_DIR` · `TZ` · `TRUST_PROXY` · `COOKIE_SECURE` · `SESION_INACTIVIDAD_MIN` · `SESION_MAX_HORAS` · `ADMIN_PASSWORD` · `ADMIN_USER` (solo con base vacía) · `SIN_DATOS` (explicadas en `.env.example`). · `GOOGLE_CLIENT_ID` · `GOOGLE_CLIENT_SECRET` · `PUBLIC_URL` · `CLAVE_CIFRADO`

---

## 5. Pruebas ejecutadas

| Batería | Cómo se ejecuta | Resultado |
|---|---|---|
| **API y seguridad** (40 pruebas, incluido el flujo de Google con un Google simulado y la eliminación de proveedores) | `npm test` | **40 de 40 pasaron** (`docs/resultado-pruebas-api.txt`) |
| **Navegador real, 15 escenarios** (18 comprobaciones) | `python tests/e2e/escenarios.py` (requiere Python, `pip install playwright pillow`, `playwright install chromium`) | **18 de 18 pasaron** (`docs/resultado-pruebas-e2e.txt`) |
| **Navegador real: correo y Google** (14 comprobaciones) | `python tests/e2e/google.py` | **14 de 14 pasaron** (`docs/resultado-pruebas-google.txt`) |
| **Navegador real: proveedores** (12 comprobaciones) | `python tests/e2e/proveedores.py` | **12 de 12 pasaron** (`docs/resultado-pruebas-proveedores.txt`) |

Los 15 escenarios pedidos: (1) empleado entra solo a ventas, (2) selecciona la panceta, (3) 5.5 kg = S/ 101.75, (4) cliente registrado, (5) otro producto (total S/ 250.75), (6) confirma, (7) stock descontado, (8) administrador entra por su pantalla, (9) ve la venta, (10) cambia el precio, (11) el empleado lo ve, (12) sube una foto y aparece, (13) crea un empleado con permisos, (14) el servidor bloquea accesos no autorizados, (15) reinicia el servidor y todo persiste. **Todos pasaron.**

Además, para comprobar que las pruebas de Google **sí detectan fallos**, se rompió a propósito cada control (state, rol, audiencia, nonce, emisor, correo sin verificar, cuenta suspendida y anulación de la clave temporal) y en los 8 casos alguna prueba falló. También se verificó: `npm run limpiar` sobre una base ya usada (con ventas, cuentas, una foto subida y un precio cambiado) conservando catálogo, precios, fotos, stock y configuración; la instalación nueva con el mismo stock de siempre; la migración desde la versión anterior sin pérdida de datos; y que ningún módulo da error con la base vacía.

**No se pudo ejecutar / no se verificó:** la conexión con el **Google real** (solo con un Google simulado); el despliegue real en Railway; los gráficos de Chart.js, la exportación `.xlsx` y las fuentes Barlow necesitan internet (en las pruebas se sustituyeron los gráficos por un reemplazo mínimo, así que **conviene que los revises con conexión**); la impresión física del comprobante; el contraste de colores no se midió con herramienta.

---

## 6. Alcance frente a tu pedido

### Hecho
Dos accesos independientes con animaciones y movimiento reducido · panel de empleados tipo punto de venta con fotos/ilustraciones, búsqueda, categorías, kilos decimales (0.250 … 1,000 kg y toneladas), cliente con registro rápido y consumidor final, carrito, descuentos autorizados, método de pago, revisión y confirmación transaccional, comprobante interno, historial y anulación con permiso · panel de administración con 13 módulos · empleados con cuenta y permisos · productos (agregar, editar, desactivar, actualizar precio, cambiar imagen, historial de precios) · categorías ordenables · inventario con kardex, ajustes y mermas con motivo · compras con kilos decimales · auditoría y registro de accesos · configuración de empresa, IGV (incluido o no, sin cobrarlo dos veces), métodos de pago, unidades, turnos y **personalización** de colores, logotipo e imágenes · migración de datos · preparación para publicar.

### Parcial o distinto a lo pedido
- **Fotografías reales:** no tengo acceso a fotos ni al PDF del catálogo (no llegó en esta conversación). Usé la lista de productos y precios que ya tenías. Cada corte muestra una **ilustración referencial** marcada como tal hasta que subas la foto real desde *Gestión de productos*. Las 8 ilustraciones son una por categoría, no por producto.
- **Productos sin precio o unidad confirmados** («Cerdo por entero», «Lomo de cerdo congelado»): aparecen como *Precio por confirmar* y **no se pueden vender** hasta que el administrador los complete. «Bife de lomo de cerdo» se incluyó con una nota de posible duplicado de «Bife de lomo».
- **Consumidor final:** tope de venta de S/ 700 configurable (según la regla habitual para boletas sin identificar; **confírmalo con tu contador**).
- **Comprobante:** es **interno**; no es factura ni boleta electrónica de SUNAT. «Descargar» se hace desde el diálogo de impresión (*Guardar como PDF*).
- **Turno del empleado:** se calcula por la hora según los turnos configurados; no hay horarios asignados por persona.
- **Dashboard:** conserva su estilo propio (etiquetas colgantes); toma de tus referencias el menú rojo, las tarjetas claras y el punto de venta con tarjetas de producto.

### Pendiente (no implementado)
**Cotizaciones** · **lotes y vencimientos** · **varios almacenes y transferencias** · control de temperatura · catálogo público y carrito de cotización · importación del PDF · PostgreSQL (sigue SQLite, apta para una sola instancia con volumen) · respaldos automáticos · sucursales · verificación en dos pasos propia (se apoya en la de Google) · envío de correos (no se mandan contraseñas ni enlaces por email) · facturación electrónica.

---

## 7. Estructura

```
server/   server.js (rutas y seguridad) · api_admin.js · api_pos.js · ventas.js (cálculo e inventario) · google.js (acceso con Google) · secretos.js (cifrado) · seguridad.js · db.js (migraciones) · seed.js · limpieza.js (personal y limpieza de datos de ejemplo) · schema.sql
public/   comun/ (estilos y utilidades) · empleados/ (login + punto de venta) · admin/ (login + panel) · img/ (logotipos e ilustraciones)
tests/    api.test.js (40) · e2e/escenarios.py (15 escenarios) · e2e/google.py (correo y Google) · e2e/proveedores.py
scripts/  clave.js (recuperar acceso) · limpiar.js (dejar solo catálogo e inventario)      docs/  er.mmd (diagrama) y resultados de pruebas
```
Requiere Node 22.13+ (usa `node:sqlite` y `node:crypto`). Sin dependencias externas.
