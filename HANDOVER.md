# DayZ Nahrungsguide – Handover

## Projekt

- **Lokaler Pfad:** `/Users/lakestudio/localhost/claude/apps/dayz-food/`
- **Repo:** https://github.com/b0li/dayz-food
- **Live:** https://b0li.github.io/dayz-food/
- **Stack:** Single `index.html` – Vanilla JS, kein Build-Step, keine Dependencies

---

## Design System

### CSS Custom Properties

```css
:root {
    --bg:        #14150f;
    --bg-panel:  rgba( 20, 22, 18, 0.82 );
    --bg-row:    rgba( 28, 31, 26, 0.55 );
    --line:      rgba( 120, 130, 110, 0.18 );
    --text:      #d6d8cf;
    --text-dim:  #8b9082;
    --text-head: #e8eae2;
    --energy:    #d9a441;
    --water:     #5aa9d6;
    --danger:    #c8503c;
    --accent:    #aeb89a;
}
```

### Body-Background

```css
body {
    background-color: var( --bg );
    background-image:
        linear-gradient( rgba( 16, 17, 13, 0.85 ), rgba( 16, 17, 13, 0.93 ) ),
        radial-gradient( circle at 30% 0%, #2c3327 0%, #14150f 60% );
    background-attachment: fixed;
}
```

### Typografie

- **Font-Stack:** `"Segoe UI", Roboto, Oxygen, Ubuntu, sans-serif`
- **Base:** 16px / line-height 1.4
- Kein Google Fonts – bewusst system fonts für Performance

| Element | Font-Size | Weight | Color | Besonderheit |
|---|---|---|---|---|
| Item-Name | 14px | 600 | --text-head | uppercase, letter-spacing 0.5px |
| Item EN-Name | 10px | 400 | --text-dim | italic |
| Kategorie | 10px | 400 | --text-dim | uppercase, letter-spacing 0.5px |
| Stat-Wert | 17px | 700 | --energy / --water | je nach Typ |
| Stat-Label | 9px | 400 | --text-dim | uppercase, letter-spacing 0.5px |
| Chip / Button | 12px | 600 | --text-dim | |
| Sortbar | 12px | 600 | --text-dim | |
| State-Button | 11px | 700 | --text-dim | uppercase, letter-spacing 0.5px |

---

## PWA

### Meta-Tags (index.html)

```html
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="theme-color" content="#14150f">
<link rel="manifest" href="manifest.json">
<link rel="apple-touch-icon" href="icon-192.png">
```

### manifest.json

```json
{
    "name": "DayZ Nahrungsguide",
    "short_name": "DayZ Food",
    "background_color": "#14150f",
    "theme_color": "#14150f",
    "display": "standalone",
    "orientation": "portrait",
    "start_url": "./index.html"
}
```

### Icons

- `icon-192.png` (192×192) und `icon-512.png` (512×512)
- Generiert aus `assets/img/logo.png` (2000×895, hat Alpha)
- Logo zentriert auf `#14150f` Hintergrund via ffmpeg:

```bash
ffmpeg -y \
  -f lavfi -i color=c=0x14150f:size=192x192:rate=1 \
  -i assets/img/logo.png \
  -filter_complex "[1:v]scale=172:-1[logo];[0:v][logo]overlay=(W-w)/2:(H-h)/2:format=auto,trim=duration=0.04" \
  -frames:v 1 -update 1 icon-192.png

# 512er: scale=460:-1, size=512x512
```

### Service Worker (`sw.js`)

- Cache-Name: `dayz-food-v1` → bei Updates auf `v2`, `v3` etc. erhöhen
- Cached: `index.html`, `manifest.json`, beide Icons, `assets/img/logo.png`
- Strategie: network-first für HTML, cache-first für Assets

---

## App-Struktur

### Dateistruktur

```
dayz-food/
├── index.html          # komplette App (HTML + CSS + JS in einer Datei)
├── manifest.json
├── sw.js
├── icon-192.png
├── icon-512.png
├── assets/
│   └── img/
│       └── logo.png    # DayZ-Logo, 2000×895px, mit Alpha
└── HANDOVER.md
```

### State-Objekt (JS)

```js
const state = {
    lang:      'de',   // 'de' | 'en'
    cat:       'all',  // 'all' | 'canned' | 'drinks' | 'dry' | 'meat' | 'fish' | 'produce' | 'mushroom'
    itemState: 'raw',  // 'raw' | 'baked' | 'boiled' | 'dried' | 'burned' | 'rotten'
    sort:      'name', // 'name' | 'energy' | 'water'
    query:     ''
};
```

### Item-Datenstruktur

```js
{
    en: 'Canned Bacon',         // In-Game-Name (immer angezeigt)
    de: 'Speck (Dose)',         // Deutscher Name
    cat: 'canned',              // Kategorie-Key
    danger: 'toxic',            // optional: 'toxic' | 'poison' | 'hallu'
    states: {
        raw:    [ kcal, ml ],   // null = Zustand existiert nicht für dieses Item
        baked:  [ kcal, ml ],
        boiled: [ kcal, ml ],
        dried:  [ kcal, ml ],
        burned: [ kcal, ml ],
        rotten: [ kcal, ml ]
    }
}
```

Konserven und Getränke nutzen `single( energy, water )` → `{ raw: [ energy, water ] }`.

### Kategorien (CAT_ORDER)

`canned` → `drinks` → `dry` → `meat` → `fish` → `produce` → `mushroom`

### Zustände (STATE_ORDER)

`raw` → `baked` → `boiled` → `dried` → `burned` → `rotten`

---

## Features

- **Sprachauswahl DE/EN** – Browser-Sprache wird automatisch erkannt (`detectLang()`)
- **Kategorie-Filter** als Chips
- **Zustands-Umschalter** (roh/gebacken/etc.) – nicht verfügbare Zustände werden ausgegraut (opacity 0.35)
- **Sortierung** nach Name / Energie ↓ / Wasser ↓
- **Suche** über deutschen und englischen Namen
- **Danger-Tags** für toxische, giftige und halluzinogene Items
- **iOS Install-Banner** (erscheint nur in iOS Safari, nicht im Standalone-Modus)

---

## Deployment

```bash
# GitHub Pages aktiv (main branch, root /)
git add .
git commit -m "..."
git push
# → automatisch live auf https://b0li.github.io/dayz-food/
```

---

## Datenquelle

Werte aus der **mydayz.eu Food Comparison Table, Build 1.29**.  
Energie in kcal, Wasser in ml – jeweils pro vollem Item.
