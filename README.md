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

Sin esto, lo que edites vive solo en el navegador donde lo editaste. Con esto, cada cambio se
commitea a `datos.json` y aparece en el celular y en cualquier navegador.

**Leer no necesita nada.** En cualquier dispositivo abres la URL y ves tus sitios: la página
lee `datos.json` del repo en cada carga. El token solo hace falta para **escribir** desde ese
dispositivo.

**Una sola vez por dispositivo que vaya a editar:**

1. Abre https://github.com/settings/personal-access-tokens/new
2. Nombre: `inicio`. Expiración: la que quieras (o sin expirar).
3. **Repository access** → Only select repositories → `inicio`.
4. **Permissions** → Repository permissions → **Contents: Read and write**. Nada más.
5. Generate token y copia el `github_pat_...`.
6. En la página, botón de nube (abajo, junto al tema) → pega el token → **conectar**.

También se puede con un enlace, sin abrir el diálogo:

```
https://sanluispropublicidad-ai.github.io/inicio/#nube=github_pat_...
```

Guarda el token, limpia la barra de direcciones y conecta. Útil para configurar el celular sin
teclear nada.

Al conectar te muestra cuántos sitios hay en el repo y cuántos aquí. Elige **usar los del
repo** o **subir los míos**.

Desde ahí, cada alta, edición, borrado o arrastre se sube solo (1.4 s después del último
cambio) con un commit `datos: fecha`. El punto del botón dice el estado: verde al día, azul
subiendo, rojo error.

**Qué pasa si dos dispositivos editan a la vez:** el segundo recibe `conflicto` (409/422),
relee el `sha` nuevo, **fusiona** —une categorías por nombre y sitios por URL, sin duplicar— y
vuelve a escribir. Verificado con 8 pruebas sobre la función de fusión.

Límite conocido de la fusión: sin marcas de tiempo, un sitio que borraste en un dispositivo
puede reaparecer si el otro todavía lo tenía. El historial de commits del repo permite ver y
deshacer cualquier caso. Si algún día molesta, se arregla con lápidas (`borrado: true`) en el
JSON.

**El token vive en `localStorage` de ese navegador.** Es un token fine-grained limitado a ese
repo y a Contents: no puede tocar nada más de tu cuenta. Si lo pegas en una computadora
prestada, bórralo al terminar (borrar datos del sitio).

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

```
bash desplegar.sh
```

Copia los archivos a un temporal, hace commit y fuerza el push al repo público
`sanluispropublicidad-ai/inicio`. Pages reconstruye en ~40 s. El repo `OOAZ` es privado y no
puede servir Pages gratis; por eso el deploy vive aparte, igual que `mickey-arcade`.

## Archivos

| Archivo | Qué es |
|---|---|
| `index.html` | Todo: estilos, lógica, interfaz. |
| `datos.json` | Tus categorías y sitios. |
| `sw.js` | Service worker: funciona sin internet. |
| `manifest.webmanifest` | Para instalarla como app. |
| `icon.svg` | Icono. |

## Detalles técnicos

- Sin frameworks, sin npm, sin build. Se abre y funciona.
- Favicons vía `google.com/s2/favicons` — sin API key. Si falla, cae a la inicial del nombre con color derivado del dominio.
- Clima opcional vía Open-Meteo (sin key), solo si haces clic en la línea del clima y concedes ubicación.
- Tema oscuro por defecto, respeta `prefers-color-scheme`, toggle manual persistente.
- Funciona desde `file://` también, aunque sin service worker ni `datos.json` remoto.
