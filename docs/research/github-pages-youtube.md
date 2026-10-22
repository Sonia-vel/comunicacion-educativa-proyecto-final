# Publicar la WebQuest en GitHub Pages con videos de YouTube incrustados

Investigación para el ticket #4 (mapa #1). Fecha de consulta: 22 de octubre de 2026.
Fuentes: solo documentación oficial de GitHub y de YouTube/Google, citada en cada punto.

Situación actual: el repo `Sonia-vel/comunicacion-educativa-proyecto-final` es **privado**, pertenece a una **cuenta personal** (no a una organización) y el sitio es un solo `index.html` en la raíz.

## Respuesta corta

- Con el plan **Free** de una cuenta personal, GitHub Pages **solo funciona en repos públicos**. Para publicar hay que (a) hacer público el repo, o (b) tener **GitHub Pro**, que es gratis con el GitHub Student Developer Pack.
- Aunque el repo sea privado, **el sitio publicado siempre es público** en internet. Solo GitHub Enterprise Cloud (organizaciones) permite sitios con acceso restringido.
- La forma de incrustar los videos que ya usa `index.html` (iframe a `youtube-nocookie.com`, ancho 100 %, `aspect-ratio: 16/9`) es correcta. Conviene agregar `referrerpolicy="strict-origin-when-cross-origin"` al iframe.
- Los videos **no listados** se pueden ver con el enlace. Hay que dejar activada la opción "Permitir insertar" y **no** ponerles restricción de edad.
- Videos para sexto de primaria: lo más seguro es marcarlos como **"contenido para niños"**. Eso desactiva comentarios, tarjetas, pantallas finales, miniplayer y anuncios personalizados, y para esta WebQuest no se pierde nada importante.

**Recomendación:** hacer público el repo y publicar desde `main` en la raíz (`/`). El sitio va a ser público de todas formas, el contenido es material educativo sin datos sensibles, y no hay que verificarse como estudiante ni esperar la aprobación. Si el equipo quiere que los borradores (guiones, notas de agentes) no se vean, la alternativa es solicitar el Student Developer Pack (GitHub Pro gratis) o publicar desde un segundo repo público que solo tenga `index.html`.

## 1. GitHub Pages y repos privados según el plan

| Plan | Pages en repo público | Pages en repo privado | Sitio con acceso restringido |
|---|---|---|---|
| Free (cuenta personal) | Sí | **No** | No |
| Pro (cuenta personal) | Sí | Sí | No |
| Team (organización) | Sí | Sí | No |
| Enterprise Cloud (organización) | Sí | Sí | Sí |

- Free: "GitHub Pages in public repositories". Pro y Team incluyen "GitHub Pages" entre las herramientas para repos privados. Nota: "To publish a GitHub Pages site privately, you need to have an organization account... your organization must use GitHub Enterprise Cloud." Fuente: [GitHub's plans](https://docs.github.com/en/get-started/learning-about-github/githubs-plans).
- "GitHub Pages sites are publicly available on the internet, even if the repository for the site is private." Fuente: [Creating a GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site).
- "To publish a GitHub Pages site privately, your organization must use GitHub Enterprise Cloud." Un sitio privado "can only be accessed by people with read access to the repository". Fuente: [Changing the visibility of your GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/changing-the-visibility-of-your-github-pages-site).
- Si un repo con Pages vuelve a ser privado en un plan Free, "any published GitHub Pages site will be automatically unpublished". Fuente: [Setting repository visibility](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility).

Límites que aplican, pero que esta WebQuest no se acerca a rebasar: sitio publicado de 1 GB como máximo, 100 GB/mes de ancho de banda (límite suave), 10 compilaciones por hora (límite suave) y un tiempo máximo de 10 minutos por despliegue. Pages no se puede usar como hosting de un negocio. Fuente: [GitHub Pages limits](https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits).

## 2. Opciones

### A. Hacer público el repo (recomendada)

Pasos ([Setting repository visibility](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility)):

1. En el repo, abrir **Settings**.
2. En "Danger Zone", hacer clic en **Change visibility** y elegir público.
3. Confirmar el nombre del repo, hacer clic en **I have read and understand these effects** y luego en **Make this repository public**.

Consecuencias que documenta GitHub: cualquier persona puede ver y hacer fork del código, el historial y los registros de Actions quedan visibles, se borran estrellas y observadores, y se desactivan los rulesets de push. Para este repo la consecuencia que importa es que **todo el historial de commits y todos los archivos** (`GLOSSARY.md`, `docs/agents/`, `CLAUDE.md`) se vuelven públicos. Antes de cambiar la visibilidad hay que revisar que no haya datos personales del alumnado ni de las demás personas del equipo.

### B. GitHub Student Developer Pack (GitHub Pro gratis)

- El pack ofrece "Free GitHub Pro while you are a student". Fuente: [education.github.com/pack](https://education.github.com/pack).
- Requisito: ser estudiante o docente de una "accredited educational institution" y solicitar la verificación en GitHub Education. Fuente: [About GitHub Education for students](https://docs.github.com/en/education/about-github-education/github-education-for-students/about-github-education-for-students). La UPN debería cumplir el requisito, pero la aprobación no es inmediata y no hay un plazo documentado. Con la entrega el 17 de noviembre, conviene solicitarlo cuanto antes si se elige esta vía.
- Con Pro, el repo puede seguir privado, pero **el sitio se publica igual de forma abierta** (ver sección 1). Esta opción solo oculta el código y el historial.

### C. Otras alternativas

- **Segundo repo público** solo con `index.html` (por ejemplo `guardianas-ecosistema`). El repo de trabajo sigue privado. Costo: hay que copiar `index.html` cada vez que cambie. La URL sería `https://sonia-vel.github.io/<nombre-repo>/`.
- Otros hosting estáticos gratuitos (Netlify, Cloudflare Pages, Vercel) también pueden publicar desde repos privados. **No se verificaron** en sus fuentes primarias para esta investigación. Además, sumarían otra cuenta y otro servicio al proyecto.

## 3. Pasos para activar Pages (una vez resuelto el plan o la visibilidad)

Fuente: [Configuring a publishing source](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site).

1. En el repo, ir a **Settings** y luego a **Pages** (sección "Code, planning, and automation").
2. En "Build and deployment", elegir **Source: Deploy from a branch**.
3. Elegir la rama `main` y la carpeta `/ (root)`.
4. Hacer clic en **Save**.
5. El sitio queda en `https://sonia-vel.github.io/comunicacion-educativa-proyecto-final/` (formato `<usuario>.github.io/<repo>/`, según [Creating a GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site)).

Notas: el commit que dispara la publicación lo tiene que hacer alguien con permisos de administración y correo verificado. Los commits hechos por un workflow con `GITHUB_TOKEN` no disparan una compilación. La fuente no indica cuánto tarda en estar en línea. Sin `.nojekyll`, Jekyll procesa el sitio, pero un `index.html` estático se publica igual. Agregar un `.nojekyll` vacío es opcional y evita que Jekyll ignore archivos o carpetas que empiezan con `_`.

## 4. Incrustar videos de YouTube no listados

**Videos no listados.** "Can be seen and shared by anyone with the link". No aparecen en el canal ni en la búsqueda, y no hace falta iniciar sesión para verlos. Fuente: [Change video privacy settings](https://support.google.com/youtube/answer/157177). La página no menciona la inserción en otros sitios. Lo que sí documenta YouTube es que quien sube un video puede **desactivar la inserción** (YouTube Studio > Contenido > Detalles > Mostrar más > "Permitir insertar"), así que hay que dejarla **activada**. Además, "Age-restricted videos can't be watched on most 3rd party websites". Fuente: [Embed videos & playlists](https://support.google.com/youtube/answer/171780).

**Modo de privacidad mejorada.** Se cambia el dominio `www.youtube.com` por `www.youtube-nocookie.com` en el código de inserción. Así las vistas insertadas no influyen en lo que YouTube le recomienda después a quien ve la página, y los anuncios no son personalizados. Fuente: [Embed videos & playlists](https://support.google.com/youtube/answer/171780).

**Requisitos del reproductor** ([Required Minimum Functionality](https://developers.google.com/youtube/terms/required-minimum-functionality), [Player parameters](https://developers.google.com/youtube/player_parameters)):

- Viewport mínimo de 200 × 200 px. Para 16:9 se recomienda al menos 480 × 270 px.
- El reproductor necesita recibir el encabezado `Referer`. No hay que suprimirlo, y YouTube recomienda `strict-origin-when-cross-origin`.
- `rel=0` ya no oculta los videos relacionados, solo los limita al mismo canal. `playsinline=1` reproduce el video dentro de la página en iOS en vez de abrirlo en pantalla completa. `start=N` empieza el video en el segundo N.

**Código recomendado** (adaptable a celular):

```html
<div class="video">
  <iframe
    src="https://www.youtube-nocookie.com/embed/VIDEO_ID?rel=0&playsinline=1"
    title="Video del experimento: ..."
    allow="accelerometer; encrypted-media; gyroscope; picture-in-picture"
    referrerpolicy="strict-origin-when-cross-origin"
    allowfullscreen></iframe>
</div>
<style>
  .video iframe { display:block; width:100%; aspect-ratio:16/9; border:0; }
</style>
```

**Estado de `index.html`:** ya genera el iframe con `youtube-nocookie.com`, `title` accesible, `width:100%` y `aspect-ratio:16/9`, además de un enlace de respaldo para abrir el video en YouTube. Solo falta agregar `referrerpolicy="strict-origin-when-cross-origin"` (y, si se quiere, `?rel=0&playsinline=1`) en la línea que arma el iframe. Para cargar cada video basta con pegar su URL no listada en el objeto `VIDEOS`.

## 5. "Contenido para niños" (COPPA)

- Un video es "made for kids" si las niñas y los niños son el público principal, o si es para público mixto y la infancia es uno de los públicos a los que se dirige. "It is your legal responsibility to comply with COPPA and/or other applicable laws and designate your content accurately." "Do not rely on our systems to set your audience for you." Fuente: [Determine if your content is made for kids](https://support.google.com/youtube/answer/9528076).
- En videos marcados como para niños se desactivan, entre otras funciones: comentarios, tarjetas y pantallas finales, marcas de agua, botón de notificaciones, publicidad personalizada, reproducción en miniplayer, "Guardar en playlist" y "Ver más tarde", y el chat en vivo. Fuente: [Features unavailable on made for kids content](https://support.google.com/youtube/answer/9527654).
- Los sitios **dirigidos a niñas y niños** que insertan videos de YouTube deben marcarse como tales con las herramientas de Google. Eso desactiva los anuncios personalizados y algunas funciones del reproductor insertado. Fuente: [Embed videos & playlists](https://support.google.com/youtube/answer/171780).

**Implicaciones para la WebQuest:** los tres videos están dirigidos a sexto de primaria, así que deben marcarse como **"Sí, es contenido para niños"**. Las restricciones no afectan la inserción en sí. Lo que se pierde son las tarjetas, las pantallas finales, los comentarios y "Ver más tarde", y nada de eso hace falta, porque las actividades viven en la WebQuest. **No hay que poner restricción de edad**, porque bloquearía la reproducción insertada. La guía de la sección 5 sobre sitios dirigidos a la infancia aplica sobre todo a sitios que muestran publicidad. Esta WebQuest no tiene anuncios ni cuentas, y con `youtube-nocookie` ya se evitan los anuncios personalizados. No se encontró en las fuentes primarias consultadas un paso obligatorio adicional para un sitio así.

## Preguntas abiertas

- Si el equipo acepta que `docs/agents/`, `CLAUDE.md` y el historial de commits queden públicos (decide si la opción A sirve o si conviene B o C).
- Quién sube los videos y desde qué cuenta de YouTube. Esa persona es la responsable de marcar la audiencia.
