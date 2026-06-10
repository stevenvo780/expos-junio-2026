# Exposiciones — junio 2026

Dos presentaciones académicas como decks web autocontenidos en **reveal.js 5.x** (CDN), tema *night*, tipografía serif (EB Garamond), notas del ponente y soporte LaTeX vía MathJax.

| Deck | Carpeta | Tema | Fecha |
|------|---------|------|-------|
| **Platón** | [`platon/`](platon/index.html) | Refutación de Simmias y Cebes · La Teoría de las Formas (*Fedón* 84c–102a) | jue 11 jun 2026 |
| **Ciudad** | [`ciudad/`](ciudad/index.html) | Sobre el límite de la IA en el conocimiento urbano (con Yuk Hui) | vie 12 jun 2026 |

## Preview local

Los decks usan plugins de reveal.js (notas, math) que requieren servirse por HTTP — **no** funcionan abriendo el `file://` directamente.

```bash
cd /workspace/expos-junio-2026
python3 -m http.server 8099
# luego, en el navegador:
#   http://localhost:8099/platon/
#   http://localhost:8099/ciudad/
```

### Atajos de teclado (reveal.js)

- **`S`** — abre la vista del ponente (*speaker notes*) en una ventana aparte con el guion hablado por slide + temporizador. (Requiere servirse por HTTP y permitir pop-ups.)
- **`F`** — pantalla completa · **`O`** / **`Esc`** — vista general (overview) · **`B`** — pantalla en negro.
- **`←` / `→` / `Espacio`** — navegar. Los bullets aparecen de a uno (fragments).
- **`?`** — ayuda con todos los atajos.

## Estructura

```
expos-junio-2026/
├── README.md
├── STATUS.json          # resumen de build/verificación
├── platon/
│   └── index.html       # deck autocontenido (10 slides)
└── ciudad/
    └── index.html       # deck autocontenido (10 slides)
```

Cada `index.html` es **self-contained**: solo depende de CDNs públicos (jsDelivr para reveal.js + MathJax, Google Fonts para EB Garamond). No hay build step ni `node_modules`.

## Deploy estático en Vercel

Cada carpeta es un sitio estático independiente. Dos opciones:

**A) Un proyecto por deck** (recomendado, URLs limpias):

- *Root Directory* = `platon` (o `ciudad`)
- *Framework Preset* = **Other** · *Build Command* = vacío · *Output Directory* = `.` (la propia carpeta)
- El `index.html` se sirve en la raíz del dominio.

**B) Un solo proyecto con ambos decks:**

- *Root Directory* = `expos-junio-2026` · *Output Directory* = `.`
- Rutas: `/platon/` y `/ciudad/`.

Con la CLI:

```bash
cd platon && vercel --prod   # o: vercel deploy
```

> No se incluye `git push` ni deploy en este entregable (sin credenciales): build y verificación **local** únicamente.

## Notas técnicas

- **Versión reveal.js fijada** a `5.1.0` vía jsDelivr para reproducibilidad.
- **MathJax 3** se carga a través del plugin `RevealMath.MathJax3`; los términos griegos (ἁρμονία, μέθεξις, ἀθάνατον…) van como texto Unicode (EB Garamond trae griego politónico) y solo el esquema lógico del argumento final usa LaTeX (`\[ … \]`).
- **Subresource Integrity (SRI):** los `<script>`/`<link>` de CDN no llevan `integrity=`. Es una decisión consciente: el plugin de math de reveal carga MathJax internamente (sin punto donde fijar el hash) y las versiones se pueden re-anclar. Para un endurecimiento de producción, fijar `integrity="sha384-…" crossorigin="anonymous"` en `reveal.css`, `night.css`, `reveal.js`, `notes.js` y `math.js`, o **vendorizar** los assets (descargarlos a `vendor/`) para un deck 100% offline.
- Para un paquete totalmente offline (aula sin internet), descargar reveal.js + MathJax a una carpeta `vendor/` y reemplazar las URLs de CDN por rutas relativas.
