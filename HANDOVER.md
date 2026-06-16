# WebP Converter – Handover

## Projekt
- **Lokaler Pfad:** `/Users/lakestudio/localhost/claude/apps/webp-converter/`
- **Repo:** https://github.com/b0li/webp-converter
- **Live:** https://b0li.github.io/webp-converter/
- **Stack:** Single `index.html` – Vanilla JS, kein Build-Step, keine Dependencies außer JSZip (CDN)

---

## Design System (identisch übernehmen)

### CSS Custom Properties
```css
:root {
    --bg: #1a1d23;
    --surface: #22262e;
    --surface-2: #2a2f39;
    --border: #333844;
    --accent: #d4a55a;
    --accent-dim: rgba( 212, 165, 90, 0.15 );
    --green: #6abf69;
    --red: #e06c6c;
    --text: #e8eaf0;
    --text-dim: #8a909e;
    --radius: 10px;
}
```

### Fonts
```html
@import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@400;600;700&display=swap');
```
- **Headings (h1):** `DM Serif Display`, font-weight 400, font-size 2rem, color `var(--accent)`
- **Body:** `DM Sans`, system-ui als Fallback

### Typografie-Skala
| Element | Font-Size | Weight | Color |
|---|---|---|---|
| h1 | 2rem | 400 (Serif) | --accent |
| Subtitle | 0.9rem | 400 | --text-dim |
| Label / Hint | 0.85rem | 400 | --text-dim |
| Body | 0.9rem | 400/600 | --text |
| Meta / Badge | 0.78rem | 400 | --text-dim |

### Body-Layout
```css
body {
    background: var( --bg );
    color: var( --text );
    font-family: 'DM Sans', system-ui, sans-serif;
    min-height: 100vh;
    padding: 32px 16px 64px;
}
```

---

## UI-Komponenten

### Surface-Karten (Controls, Dateiliste)
```css
background: var( --surface );
border-radius: var( --radius );  /* 10px */
border: 1px solid var( --border );
padding: 14px 16px;
```

### Buttons
```css
/* Primary / Accent */
.btn--accent { background: var( --accent ); color: #1a1d23; }

/* Ghost */
.btn--ghost { background: var( --surface-2 ); color: var( --text-dim ); border: 1px solid var( --border ); }

/* Download (grün) */
.btn--download { background: var( --green ); color: #1a1d23; }

/* Danger */
.btn--danger { background: transparent; color: var( --red ); border: 1px solid var( --red ); }

/* Remove (Icon-Button) */
.btn--remove { background: transparent; border: none; color: var( --text-dim ); }
.btn--remove:hover { color: var( --red ); }

/* Shared */
.btn { padding: 8px 18px; border-radius: 6px; font-size: 0.85rem; font-weight: 600; }
.btn:not(:disabled):hover { opacity: 0.85; }
```

### Drop-Zone
```css
border: 2px dashed var( --border );
border-radius: var( --radius );
background: var( --surface );
/* Hover/Drag-State: */
border-color: var( --accent );
background: var( --accent-dim );
```

### Range Slider
```css
input[type="range"] {
    -webkit-appearance: none;
    height: 4px;
    border-radius: 2px;
    background: var( --border );
}
/* Thumb: 18×18px, border-radius 50%, background var(--accent) */
```

### Number Input
```css
input[type="number"] {
    width: 80px;
    background: var( --surface-2 );
    border: 1px solid var( --border );
    border-radius: 6px;
    color: var( --text );
    font-size: 0.9rem;
    padding: 5px 8px;
}
input[type="number"]:focus { border-color: var( --accent ); }
```

### Status-Farben
```css
.status--processing { color: var( --accent ); }
.status--done       { color: var( --green ); }
.status--error      { color: var( --red ); }
```

### Progress Bar
```css
height: 3px; background: var( --border ); border-radius: 2px;
/* Fill: background var(--accent) */
```

---

## Features der bestehenden App

- Drag & Drop + Click-to-select (mehrere Dateien gleichzeitig)
- Qualitäts-Slider (1–100, Default: 70)
- Optionales Breite-Feld in px – skaliert nur herunter, nie hoch; Hinweis „Nicht vergrößert" bei zu kleinem Bild
- Canvas API → `toBlob('image/webp', quality)` für Konvertierung im Browser (kein Upload)
- Dateiliste: Thumbnail, Dateiname, Größenvergleich (Original → WebP, % Ersparnis), Auflösung (Original → Output)
- Einzel-Download + ZIP-Batch (JSZip CDN: `cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js`)
- Fehlerbehandlung: Nicht-Bild-Dateien zeigen Fehlereintrag in der Liste

---

## Code-Style (LS-Standard)

- **Tabs** für Einrückung
- **Spaces inside Parentheses:** `document.querySelector( '.foo' )`
- **Space nach `!`:** `if ( ! el ) return;`
- **Closing Callbacks:** `});` (kein Space vor `}`)
- Leerzeilen zwischen logischen Blöcken
- Kommentare auf Englisch
- Vanilla JS, kein Framework

---

## Deployment

```bash
# GitHub Pages ist aktiv (main branch, root /)
git add index.html
git commit -m "..."
git push
# → automatisch live auf https://b0li.github.io/webp-converter/
```
