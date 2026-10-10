# Laboratorio 08: Atlas, Render y GitHub Actions

Se reutiliza la aplicación JWT de la semana 07 con autorización del profesor. Este repositorio conserva registro, perfiles y dashboards; no necesita una segunda API CRUD.

## 1. Probar con Atlas en el equipo

1. Ejecuta `npm ci` y copia `.env.example` a `.env` si el archivo todavía no existe.
2. En Atlas, Connect → Drivers → Node.js, copia la URI. Configura **MONGODB_URI** (el nombre que usa esta aplicación), con credenciales reales y la base `auth_db_s08` antes del signo `?`. No uses las bases de ejemplo ni la base de la semana 07.
3. Configura un JWT_SECRET aleatorio y, opcionalmente, las credenciales del administrador. La contraseña de Atlas y la contraseña del administrador de la aplicación son distintas.
4. Ejecuta `npm run dev`; abre `/signUp`, registra una cuenta de prueba y comprueba su colección en Compass.

Nunca subas `.env`, capturas de contraseñas ni URIs con credenciales. Las capturas heredadas en `screenshots/` muestran el diseño de la semana 07, no evidencian un despliegue de la semana 08.

## 2. Crear el servicio en Render

Conecta DAWA-S8 a un Web Service o crea un Blueprint con `render.yaml`.

| Campo | Valor |
|---|---|
| Name | express-auth-huaytalla |
| Branch | main |
| Runtime | Node |
| Build command | npm ci |
| Start command | npm start |
| Plan | Free |
| Health check | /health |
| Auto deploy | Off: GitHub Actions se encarga del despliegue |

Configura NODE_ENV=production, MONGODB_URI, JWT_SECRET, JWT_EXPIRES_IN=1h y BCRYPT_SALT_ROUNDS=10. ADMIN_EMAIL y ADMIN_PASSWORD permiten crear la cuenta de administrador en el primer arranque. Render asigna PORT automáticamente. Si usas el formulario manual, genera tu propio JWT_SECRET; el Blueprint lo genera automáticamente.

Atlas debe autorizar también las direcciones de salida del servicio de Render, disponibles en su panel Connect → Outbound. Autoriza esos rangos concretos; la IP de tu PC solo permite las conexiones locales. La conexión de Render mantiene TLS.

## 3. Activar CI/CD

1. En Settings del servicio de Render, copia Deploy Hook sin publicarlo.
2. En GitHub → Settings → Secrets and variables → Actions, crea el secreto `RENDER_DEPLOY_HOOK_URL` con esa URL.
3. Desactiva los despliegues automáticos nativos de Render para evitar duplicados y ejecuciones antes de las pruebas.
4. Un push a main ejecuta `npm ci` y `npm test` contra una MongoDB temporal en GitHub Actions. Si pasan, solicita a Render desplegar el SHA de ese commit.
5. Verifica en Render que el despliegue terminó como Live y que el commit coincide. Un workflow correcto confirma la solicitud, no que Render haya terminado.

## 4. Evidencias para el Word

- Compass conectado al clúster, con `auth_db_s08`, users y roles visibles.
- Terminal con el servidor iniciado y prueba de registro/inicio de sesión en Postman.
- Aplicación en la URL HTTPS de Render: registro, perfil y dashboards según rol.
- GitHub Actions con pruebas y solicitud de despliegue correctas.
- Render con estado Live, logs de arranque y el mismo commit de Actions.
- Enlaces del repositorio y de la aplicación publicados.

Usa datos de prueba, oculta tokens y credenciales y realiza tus propias capturas cuando esos pasos estén verificados.

Referencias: [Express en Render](https://render.com/docs/deploy-node-express-app), [Deploy Hooks](https://render.com/docs/deploy-hooks), [Blueprint](https://render.com/docs/blueprint-spec).
