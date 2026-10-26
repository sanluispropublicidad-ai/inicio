# inicio

Página de inicio personal. Tus sitios, en un solo lugar. Un solo archivo, sin build, sin dependencias.

**En vivo:** https://sanluispropublicidad-ai.github.io/inicio/

## Cómo se usa

- **Buscar:** `/` o `Ctrl+K`. Escribes y filtra al instante. `Enter` abre el primero, flechas para moverse.
- **Buscar en la web:** si nada coincide, `Enter` busca en Google. Prefijos: `g` google, `yt` youtube, `w` wikipedia, `gh` github, `ddg` duckduckgo.
- **Agregar sitio:** botón `+` o tecla `n`.
- **Editar / borrar / reordenar:** botón del lápiz o tecla `e`. Arrastra una tarjeta sobre otra para moverla de sitio o de categoría.
- **Importar marcadores de Chrome:** menú de Chrome → Marcadores → Administrador de marcadores → Exportar. Arrastra ese `.html` al botón de importar. Las carpetas se vuelven categorías.
- **Exportar:** baja `datos.json` con tu estado actual.

## Guardar en la nube (multi-dispositivo)

**Ya está conectado. No hay nada que configurar, en ningún dispositivo.**

Cada alta, edición, borrado o arrastre se guarda en `datos.json` del repo 1.2 s después del
último cambio, con un commit `datos: fecha`. El punto del botón de nube dice el estado: verde
al día, azul subiendo, rojo sin conexión (clic para reintentar).

**Cómo está montado, y por qué así:**

```
navegador  →  ooaz-inicio.vercel.app/api/datos  →  api.github.com  →  datos.json
              (token de GitHub vive aquí)          (nunca en el navegador)
```

- La función está en `api/datos.js`, desplegada en Vercel como `ooaz-inicio`.
- El token de GitHub es una variable de entorno del servidor. **Nunca llega al navegador**, así
  que no puede robarlo un script de la página ni un CDN comprometido.
- `GET` lee `datos.json` con el `sha` actual. `PUT` valida el esquema y escribe.
- El endpoint solo responde al origen de la página (`sanluispropublicidad-ai.github.io`) y solo
  escribe ese archivo de ese repo. Una URL `javascript:` en un sitio se rechaza con 400.
- Si el endpoint no responde, la página cae a leer `datos.json` de Pages: sigues viendo tus
  sitios, solo no puedes guardar hasta que vuelva.

**Qué pasa si dos dispositivos editan a la vez:** el segundo recibe `409`, relee, **fusiona**
—une categorías por nombre y sitios por URL, sin duplicar— y reescribe. Verificado con 8
pruebas sobre la función de fusión.

Límite conocido: sin marcas de tiempo, un sitio que borraste en un dispositivo puede
reaparecer si el otro todavía lo tenía. El historial de commits permite ver y deshacer
cualquier caso. Si algún día molesta, se arregla con lápidas (`borrado: true`) en el JSON.

**Si hay que cambiar el token:** `vercel env rm GH_TOKEN production` y volver a agregarlo con
`vercel env add GH_TOKEN production`, luego `vercel deploy --prod`. La página no se toca.

## Dónde viven los datos

1. `datos.json` del repo — **la fuente de verdad**. Se lee en cada carga (sin caché). Si editas
   desde la web, el commit va aquí.
2. `localStorage` — copia local para arrancar rápido y funcionar sin red. Se sobrescribe con lo
   del repo en cada carga.
3. Exportar/importar — salida manual y respaldo.

**Ojo con el orden de despliegue:** `desplegar.sh` clona el repo y **respeta el `datos.json`
remoto**. No lo pisa con el local. Si alguna vez hay que empujar el local a la fuerza, se hace
a mano y a sabiendas.

Sin nube conectada no hay escritura permanente: lo que edites se pierde al recargar (la página
avisa la primera vez). Es a propósito — un cambio que no se sube no existe para los demás.

## Ponerla como página de inicio

- **Chrome / Edge (PC):** Configuración → Al iniciar → Abrir una página específica → `https://sanluispropublicidad-ai.github.io/inicio/`
- **Chrome (Android):** menú → Ajustes → Página de inicio → URL.
- **Safari (iOS):** no permite cambiar la página de inicio, pero sí añadir a pantalla de inicio como app (funciona igual, offline incluido).

## Desplegar cambios

Dos destinos, uno para la página y otro para la API.

**Página (GitHub Pages):**

```
bash desplegar.sh
```

Clona el repo público `sanluispropublicidad-ai/inicio`, copia el código y hace push normal
—**respeta el `datos.json` remoto**, para no borrar lo que se editó desde la web—. Pages
reconstruye en ~40 s. El repo `OOAZ` es privado y no puede servir Pages gratis; por eso el
deploy vive aparte, igual que `mickey-arcade`.

**API (Vercel):**

```
VERCEL_TOKEN=... vercel deploy --prod
```

Solo hace falta si cambia `api/datos.js` o las variables de entorno. La URL es fija:
`https://ooaz-inicio.vercel.app/api/datos`.

## Archivos

| Archivo | Qué es |
|---|---|
| `index.html` | Todo el cliente: estilos, lógica, interfaz. |
| `api/datos.js` | Función serverless en Vercel. Guarda el token de GitHub y escribe `datos.json`. |
| `vercel.json` | Configuración mínima del proyecto en Vercel. |
| `datos.json` | Tus categorías y sitios (semilla; el repo es la verdad). |
| `sw.js` | Service worker: funciona sin internet. |
| `manifest.webmanifest` | Para instalarla como app. |
| `icon.svg` | Icono. |
| `desplegar.sh` | Deploy de la página a GitHub Pages. |

## Detalles técnicos

- Sin frameworks, sin npm, sin build en el cliente. Se abre y funciona.
- Favicons vía `google.com/s2/favicons` — sin API key. Si falla, cae a la inicial del nombre con color derivado del dominio.
- Clima opcional vía Open-Meteo (sin key), solo si haces clic en la línea del clima y concedes ubicación.
- Tema oscuro por defecto, respeta `prefers-color-scheme`, toggle manual persistente.
- Funciona desde `file://` también, aunque sin service worker, sin nube y sin `datos.json` remoto.
