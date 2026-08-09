# Proyecto: Mini-webapps monetizadas (Ko-fi)

## Objetivo general

Crear varias mini-webapps (calculadoras/utilidades) simples, rápidas de construir,
subirlas a un sitio propio y monetizarlas vía "invítame un café" (Ko-fi + PayPal).
Público objetivo: todo el mundo, pero fácil de operar (crear cuenta / retirar dinero)
desde Colombia.

**Prioridad:** velocidad de lanzamiento > perfección. Simples, sin backend,
HTML/JS puro, sin frameworks, sin build step.

---

## Decisiones ya tomadas

### Monetización
- **Plataforma elegida: Ko-fi** (no Buy Me a Coffee, no Patreon).
  - Motivo: Stripe NO está soportado de forma nativa en Colombia. Ko-fi sí acepta
    PayPal además de Stripe, y PayPal es fácil de crear y retirar desde Colombia.
  - Ko-fi plan gratis: 0% comisión de plataforma en tips (solo fee normal de PayPal ~2.9%+$0.30).
  - Acción pendiente del usuario: crear cuenta en ko-fi.com y conectar PayPal.
  - Reemplazar `TU_USUARIO` por el usuario real de Ko-fi en todos los botones "☕ Invítame un café".

### Stack técnico
- **HTML/JS vanilla en un solo archivo por herramienta.** Sin React, sin build,
  sin dependencias de pago. Google Fonts vía `@import` es la única dependencia externa.
- Cada herramienta vive en su propia carpeta con `index.html` dentro (ver estructura).

### Hosting / Deploy
- **Vercel**, conectado a un repo de **GitHub** (usuario ya tiene ambas cuentas).
- Deploy: importar el repo en Vercel → Framework Preset "Other" → Deploy.
  No requiere configuración de build.
- Dominio: por ahora usar el gratuito `*.vercel.app`. Dominio propio (opcional, futuro)
  se puede comprar en Namecheap (~$10-12/año).
- **IMPORTANTE:** cuando el usuario tenga la URL final de Vercel (o dominio propio),
  hay que reemplazar el placeholder `TU-DOMINIO.vercel.app` en TODOS los archivos:
  - `canonical`
  - `hreflang` (es / en / x-default)
  - Open Graph (`og:url`, `og:image`)
  - Schema.org JSON-LD (`url`)

### Estructura del sitio (Opción A: un solo repo/dominio, varias rutas)
Elegida por mejor SEO acumulado (un dominio fuerte > varios dominios débiles) y
mejor experiencia de usuario (un solo lugar con menú, no links sueltos).

```
site/
  index.html                      → HUB: menú con tarjetas a cada herramienta (5 activas)
  interes-compuesto/
    index.html                    → Calculadora de interés compuesto (LISTA)
  imc-calorias/
    index.html                    → Calculadora IMC + TDEE (LISTA)
  prestamo/
    index.html                    → Calculadora cuota de préstamo (LISTA)
  split-bill/
    index.html                    → Dividir cuenta entre personas (LISTA)
  peso-mascota/
    index.html                    → Peso ideal de mascotas, perro/gato (LISTA)
  conversor-divisas/
    index.html                    → Conversor de divisas con tasas del BCE (LISTA)
  radio-online/
    index.html                    → Directorio de radio online por país (LISTA)
  tv-en-vivo/
    index.html                    → TV en vivo, solo canales oficiales vía YouTube embed (LISTA)
  conversor-unidades/
    index.html                    → Conversor de unidades (PENDIENTE - idea futura, no confirmada aún)
```

### Cómo subir a GitHub → Vercel
1. Crear/usar repo en GitHub (ej. `mini-webapps`).
2. Subir la carpeta `site/` completa manteniendo la estructura exacta de arriba
   (el `index.html` raíz debe quedar en la raíz del repo).
3. En Vercel: "Add New" → "Project" → "Import Git Repository" → seleccionar el repo →
   Framework Preset "Other" → Deploy.
4. Vercel da una URL tipo `mini-webapps.vercel.app` en 1-2 minutos, con HTTPS automático.
5. Devolver esa URL para actualizar todos los placeholders `TU-DOMINIO.vercel.app`.

---

## Ideas de calculadoras (investigación de demanda ya hecha)

Basado en research de SEO/tráfico orgánico (calculadoras financieras y de salud
tienen tráfico masivo y recurrente; nichos específicos con baja competencia real
ganan más rápido que términos genéricos saturados por marcas grandes).

1. ✅ **Interés compuesto / ahorro** — CONSTRUIDA
2. ✅ **IMC + calorías diarias (TDEE)** — CONSTRUIDA
3. ✅ **Cuota de préstamo / crédito** — CONSTRUIDA (alta demanda en LatAm, créditos de consumo, vivienda)
4. ✅ **Split bill (dividir cuenta + propina)** — CONSTRUIDA (uso constante, viral en grupos)
5. ✅ **Peso ideal de mascotas (perro/gato)** — CONSTRUIDA (rangos por tamaño de raza, orientativo)
6. ✅ **Conversor de divisas** — CONSTRUIDA (API Frankfurter.app, datos del BCE, sin API key)
7. ✅ **Radio online gratis** — CONSTRUIDA (API Radio-Browser, directorio abierto por país)
8. ✅ **TV en vivo gratis** — CONSTRUIDA (solo canales oficiales vía YouTube Live embed, ver política de contenido abajo)
9. ⏳ **Conversor de unidades todo-en-uno** — uso diario, no confirmada como prioridad aún

---

## Sistema de diseño (aplicar a TODAS las herramientas nuevas)

### Paleta (CSS variables, ya definida — copiar tal cual)
```css
:root{
  --ink:#0F1B2B;        /* fondo, azul noche profundo */
  --paper:#F5EFE3;      /* texto principal, crema papel */
  --gold:#C9A961;       /* acento principal, dorado envejecido */
  --sage:#4A7C6E;       /* verde salvia — usar para "ganancias/positivo" */
  --terracotta:#8B4B3B; /* terracota oscuro — usar para "aportes/costo base" */
  --ink-soft:rgba(245,239,227,0.62);
  --ink-faint:rgba(245,239,227,0.32);
}
```

### Tipografía
- Display / títulos: **Fraunces** (serif con carácter), peso 600, itálica en `<em>` para el acento dorado.
- Cifras / datos: **Space Mono** (monoespaciada, look de "ticker financiero").
- Cuerpo / UI: **Inter**.
- Import: `@import url('https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,300;9..144,500;9..144,600;9..144,700&family=Space+Mono:wght@400;700&family=Inter:wght@400;500;600&display=swap');`

### Estructura visual estándar
- Header centrado: eyebrow (mono, dorado, uppercase) → H1 (Fraunces, con una palabra en `<em>` dorada) → subtítulo.
- Grid de 2 columnas (inputs a la izquierda, resultado a la derecha), colapsa a 1 columna en mobile (`<760px`).
- Panel de resultado con "signature element" visual propio de cada herramienta (en interés compuesto: gráfico de capas apiladas aporte vs. interés). Cada calculadora nueva debe tener SU PROPIO elemento visual distintivo, no reusar el mismo gráfico.
- Sección SEO crawlable al final (H2 + 2-3 FAQ) antes del footer — contenido real, visible, no oculto.
- Footer con CTA de Ko-fi + disclaimer.
- Topbar con link "← Todas las herramientas" (a `/`) + selector de idioma ES/EN.

### Responsive (ya resuelto, replicar patrón)
- `clamp()` en toda tipografía/espaciado.
- Inputs con `font-size:16px` mínimo (evita zoom automático en iOS).
- Sliders con área táctil ampliada (min 28px alto).
- Breakpoint mobile en `760px` para el grid, ajustes extra en `380px` para phones chicos.

---

## Herramientas con llamadas a APIs externas (sin backend propio)

A partir del conversor de divisas, la radio online y la TV en vivo, algunas
herramientas SÍ hacen `fetch()` a APIs públicas gratuitas desde el cliente
(siguen siendo 100% JS vanilla sin backend propio, solo que ahora con
llamadas de red). Reglas replicadas en las tres:

- Manejo de errores de red visible (mensaje + botón de reintentar), nunca
  una página en blanco si la API falla o no hay internet.
- Loading state mientras se espera la respuesta.
- Debounce (~400ms) en inputs que disparan fetch en cada tecla.

### Conversor de divisas (`/conversor-divisas/`)
- API: [Frankfurter.app](https://www.frankfurter.app/) — gratis, sin API key,
  datos de referencia del Banco Central Europeo (BCE).
- **Importante:** Frankfurter solo cubre ~30 monedas "mayores" (las que el BCE
  publica). NO incluye COP (peso colombiano) ni la mayoría de monedas
  latinoamericanas/emergentes. Los selects se pueblan dinámicamente desde
  `/currencies`, así que solo aparecen las monedas realmente soportadas.
  Default: USD → EUR.

### Radio online (`/radio-online/`)
- API: [Radio-Browser](https://www.radio-browser.info/) — directorio
  abierto/comunitario de streams de radio, gratis, sin key. Se usa el mirror
  `de1.api.radio-browser.info` directamente.
- Nota legal explícita en el footer: los streams son de terceros (las
  emisoras), agregados desde un directorio abierto. El sitio no aloja audio
  propio.

### TV en vivo (`/tv-en-vivo/`) — política de contenido
- **Solo canales que transmiten oficialmente y gratis vía su propio canal de
  YouTube**, usando el embed oficial `youtube.com/embed/live_stream?channel=ID`.
  Esto es legal porque usa la infraestructura de embed que YouTube provee
  para ese propósito exacto — el sitio no aloja ni redistribuye video.
- **Explícitamente prohibido:** listas IPTV genéricas, listas M3U de
  terceros, o retransmisiones no oficiales de canales privados con
  copyright. Si en el futuro se agregan más canales, deben cumplir esta
  misma regla (canal de noticias/servicio público con stream 24/7 oficial y
  gratuito en YouTube, channel ID verificado manualmente, no inventado).
- Canales incluidos (verificados manualmente vía `externalId` de la página
  del canal + confirmado contra el RSS feed de YouTube): NASA TV, France 24,
  DW News, euronews.
- Estructura en código: array `CHANNELS` de objetos
  `{ name, youtubeChannelId, country, category, icon }` — agregar un canal
  nuevo es solo añadir un objeto al array.

---

## Sistema de internacionalización (i18n) — replicar patrón exacto

- Detecta idioma con `navigator.language`, default a `en` si no empieza con `es`.
- Guarda preferencia en `localStorage` (`preferred-lang`) si el usuario cambia manual.
- Objeto `translations = { es: {...}, en: {...} }` con TODAS las strings de la página,
  incluyendo `docTitle`, `docDesc` (para meta tags) y `titleHtml` (para el H1 con `<em>`).
- Función `applyLang(lang)` actualiza:
  - `document.documentElement.lang`
  - `<title>`, meta description, Open Graph, Twitter Cards
  - Todos los `[data-i18n]` (textContent) y `[data-i18n-html]` (innerHTML, solo para el título)
  - Botones de idioma activos (clase `.active`)
  - Re-renderiza cálculos si aplica (formato de moneda/número cambia con el locale vía `Intl.NumberFormat`)

**Nota:** cada herramienta nueva necesita su propio diccionario `translations` completo
en español e inglés — no hay un sistema compartido entre páginas todavía (cada HTML es autónomo).

---

## SEO técnico — checklist para cada página nueva

- [ ] `<title>` con keyword principal + año (ej. "Calculadora de IMC Gratis — ... | 2026")
- [ ] `meta description` (150-160 caracteres, con keyword)
- [ ] `canonical` apuntando a su propia ruta
- [ ] `hreflang` es/en/x-default
- [ ] Open Graph completo (title, description, type, url, image, locale + locale:alternate)
- [ ] Twitter Card (`summary_large_image`)
- [ ] `theme-color` para mobile
- [ ] Schema.org JSON-LD tipo `WebApplication` (con `applicationCategory` correcta: FinanceApplication, HealthApplication, etc.)
- [ ] Schema.org JSON-LD tipo `FAQPage` con 2-3 preguntas reales (para rich snippets)
- [ ] Sección de contenido visible (no oculto) con H2 + FAQ, texto real indexable
- [ ] `aria-label`, `aria-live="polite"` en resultados, `:focus-visible` visible
- [ ] `prefers-reduced-motion` respetado en animaciones

---

## Estado actual / próximos pasos

1. ✅ Investigación de plataforma de monetización (Ko-fi ganó)
2. ✅ Investigación de demanda de calculadoras (5 ideas validadas)
3. ✅ Calculadora de interés compuesto — diseño + responsive + i18n + SEO completo
4. ✅ Calculadora de IMC + calorías (TDEE) — gauge tipo termómetro, Mifflin-St Jeor
5. ✅ Calculadora de cuota de préstamo — donut chart con conic-gradient, amortización francesa
6. ✅ Split bill (dividir cuenta + propina) — fichas/avatares dinámicos por persona
7. ✅ Peso ideal de mascotas (perro/gato) — gauge por tamaño de raza, orientativo
8. ✅ Conversor de divisas — API Frankfurter.app (BCE), tarjetas de moneda con bandera emoji + flecha animada
9. ✅ Radio online gratis — API Radio-Browser, ecualizador CSS animado en la estación sonando
10. ✅ TV en vivo gratis — solo canales oficiales vía YouTube Live embed (NASA TV, France 24, DW News, euronews)
11. ✅ Hub principal (`index.html`) con tarjetas a las 8 herramientas, todas "Disponible"
12. ✅ Repo en GitHub: https://github.com/DevMenthor90/mini-webapps
13. ✅ Desplegado en Vercel: https://mini-webapps-six.vercel.app (auto-deploy conectado al repo)
14. ✅ Placeholders `TU-DOMINIO.vercel.app` reemplazados en todos los archivos
15. ✅ Cuenta Ko-fi creada y conectada a PayPal: https://ko-fi.com/devmenthor — `TU_USUARIO` reemplazado en todos los botones
16. ⏳ Pendiente (opcional): conversor de unidades todo-en-uno, si se confirma como prioridad
17. ⏳ Pendiente (opcional): dominio propio si el proyecto gana tracción

---

## Contexto del usuario (para mantener consistencia de tono/decisiones)

- Desarrollador/arquitecto consultor (.NET/C#, Angular, SQL Server, MongoDB), usa Mac (Apple Silicon).
- Prefiere ir directo al grano, resultados accionables, no explicaciones largas innecesarias.
- Ya tiene cuentas de GitHub y Vercel.
- Español como idioma de trabajo principal — pero el producto final debe ser bilingüe ES/EN
  porque el público objetivo es global, aunque la operación (cuenta, retiro de dinero) es desde Colombia.
- Prefiere que Claude tome la decisión "más recomendada" cuando hay ambigüedad, en vez de
  preguntar demasiado — pero sí quiere que se le pregunte cuando la decisión es suya
  (ej. stack, tipo de contenido) antes de construir.
