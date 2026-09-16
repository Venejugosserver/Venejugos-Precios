# Digimarket — Lista de precios en GitHub Pages

Este sitio tiene dos páginas:

- **`index.html`** → lista de precios pública, de solo lectura. Es la que verán tus clientes.
- **`admin.html`** → panel protegido con contraseña donde editas productos/precios/cantidades y publicas los cambios directo a GitHub.

Ambas páginas leen y escriben el mismo archivo: `precios.json`.

---

## 1. Crear el repositorio

1. Ve a GitHub → **New repository** (ej. `digimarket-precios`). Puede ser público o privado (ver nota de seguridad al final).
2. Sube estos archivos a la raíz del repositorio, respetando la carpeta:
   ```
   index.html
   admin.html
   styles.css
   precios.json
   ```

## 2. Activar GitHub Pages

1. En el repo: **Settings → Pages**.
2. En "Build and deployment" elige **Deploy from a branch**.
3. Selecciona la rama `main` y carpeta `/ (root)`.
4. Guarda. En unos minutos tu sitio quedará en:
   `https://TU-USUARIO.github.io/digimarket-precios/`
   - Lista pública: esa misma URL (`index.html`)
   - Panel admin: agrega `/admin.html`

## 3. Crear el token de acceso para publicar cambios

El panel de administración necesita un token para poder escribir en tu repositorio (así es como "actualiza" GitHub).

1. Ve a **GitHub → Settings (de tu cuenta) → Developer settings → Personal access tokens → Fine-grained tokens** (o "Tokens classic").
2. Genera uno con acceso **solo a este repositorio** y permiso **Contents: Read and write**.
3. Copia el token (empieza con `github_pat_...` o `ghp_...`). Solo se muestra una vez.

⚠️ Este token funciona como una contraseña de escritura sobre tu repo. No lo compartas ni lo subas a ningún archivo del repositorio.

## 4. Configurar el panel admin

1. Abre `TU-URL/admin.html`.
2. Contraseña por defecto: **`Digimarket2026`** (ver cómo cambiarla abajo).
3. Abre "⚙ Conexión con GitHub" y llena:
   - Usuario/organización: tu usuario de GitHub
   - Repositorio: `digimarket-precios` (o el nombre que usaste)
   - Rama: `main`
   - Ruta del archivo: `precios.json`
   - Token: el que generaste en el paso 3
4. Clic en **Guardar configuración** (se guarda solo en tu navegador, se borra al cerrar la pestaña).
5. Edita precios/cantidades en la tabla y clic en **⬆ Publicar cambios en GitHub**. Esto crea un commit en tu repo y la página pública se actualiza automáticamente en 1–2 minutos.

## Actualizar todo el inventario subiendo un Excel

En la parte superior del panel admin hay una sección **"📥 Cargar productos desde Excel"**:

1. Clic en el campo de archivo y selecciona tu `.xlsx`, `.xls` o `.csv`.
2. El sistema detecta automáticamente las columnas de **producto**, **marca**, **precio** y **cantidad/stock** (reconoce nombres como "Precio (USD)", "Cantidad", "Nombre", etc.).
3. Verás una vista previa con las primeras filas y cuántos productos se detectaron.
4. Marca la casilla **"Agregar a la lista actual"** si quieres sumar productos nuevos sin borrar los existentes. Si la dejas sin marcar, el Excel **reemplaza** toda la lista.
5. Clic en **"Aplicar al panel"** — esto carga los datos en la tabla (todavía no se publica).
6. Revisa/edita lo que necesites y luego clic en **⬆ Publicar cambios en GitHub** para que el cambio quede en línea.

Si alguna columna no se detecta automáticamente, revisa que el encabezado de esa columna en tu Excel use un nombre reconocible (producto/nombre, marca, precio, cantidad/stock).

## Cómo cambiar la contraseña del panel admin

La contraseña no se guarda en texto plano, sino como un hash SHA-256, dentro de `admin.html`:

```js
const PASSWORD_HASH = '37e0f1cbe10546702a69eefe28b31f8598b84bc0e220b69621e715594034db5e';
```

Para cambiarla, obtén el SHA-256 de tu nueva contraseña (por ejemplo en la consola del navegador):

```js
async function hash(s){const b=await crypto.subtle.digest('SHA-256',new TextEncoder().encode(s));return[...new Uint8Array(b)].map(x=>x.toString(16).padStart(2,'0')).join('')}
hash('TuNuevaContraseña').then(console.log)
```

Copia el resultado y reemplaza el valor de `PASSWORD_HASH` en `admin.html`.

## Nota importante sobre seguridad

La contraseña del panel es una barrera básica pensada para que un visitante casual no vea ni toque la tabla de edición — no es seguridad de nivel empresarial, porque el código de la página es visible para cualquiera (es una página estática). La protección real de escritura la da el **token de GitHub**: sin un token válido con permiso sobre tu repo, nadie puede publicar cambios aunque conozca la contraseña.

Si quieres más seguridad:
- Usa un token de acceso **fine-grained** limitado solo a este repositorio y con expiración corta.
- Considera no enlazar `admin.html` desde ningún lugar público (solo tú conoces la URL).
- Para protección real de acceso (no solo de escritura), tendrías que mover el panel a un hosting con autenticación de servidor — GitHub Pages no lo soporta de forma nativa.
