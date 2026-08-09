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
  index.html                      → HUB: menú con tarjetas a cada herramienta
  interes-compuesto/
    index.html                    → Calculadora de interés compuesto (LISTA)
  imc-calorias/
    index.html                    → Calculadora IMC + TDEE (PENDIENTE)
  prestamo/
    index.html                    → Calculadora cuota de préstamo (PENDIENTE)
  split-bill/
    index.html                    → Dividir cuenta entre personas (PENDIENTE)
  conversor-unidades/
    index.html                    → Conversor de unidades (PENDIENTE - 5ta idea, no confirmada aún)
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
2. ⏳ **IMC + calorías diarias (TDEE)** — siguiente en la fila
3. ⏳ **Cuota de préstamo / crédito** — alta demanda en LatAm (créditos de consumo, vivienda)
4. ⏳ **Split bill (dividir cuenta + propina)** — uso constante, viral en grupos
5. ⏳ **Conversor de unidades todo-en-uno** — uso diario, no confirmada como prioridad aún

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
4. ✅ Hub principal (`index.html`) con tarjetas a las 4 herramientas (1 activa, 3 "próximamente")
5. ✅ Repo en GitHub: https://github.com/DevMenthor90/mini-webapps
6. ✅ Desplegado en Vercel: https://mini-webapps-six.vercel.app (auto-deploy conectado al repo)
7. ✅ Placeholders `TU-DOMINIO.vercel.app` reemplazados en todos los archivos
8. ✅ Cuenta Ko-fi creada y conectada a PayPal: https://ko-fi.com/devmenthor — `TU_USUARIO` reemplazado en todos los botones
9. ⏳ Pendiente: construir calculadora de IMC/calorías (siguiente en la fila)
10. ⏳ Pendiente: construir calculadora de préstamo
11. ⏳ Pendiente: construir split bill
12. ⏳ Pendiente (opcional): dominio propio si el proyecto gana tracción

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
