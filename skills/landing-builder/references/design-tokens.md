# Design Tokens

Source: [plannotator/effective-html](https://github.com/plannotator/effective-html) (Apache 2.0) + carousel-builder quality gates.

## Base Palette

```css
:root {
  --ivory:   #FAF9F5;
  --slate:   #141413;
  --clay:    #D97757;
  --oat:     #E3DACC;
  --olive:   #788C5D;
  --gray-150:#F0EEE6;
  --gray-300:#D1CFC5;
  --gray-500:#87867F;
  --gray-700:#3D3D3A;
  --white:   #FFFFFF;

  --serif: ui-serif, Georgia, "Times New Roman", Times, serif;
  --sans:  system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  --mono:  ui-monospace, "SF Mono", Menlo, Monaco, Consolas, monospace;

  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 14px;
  --radius-full: 999px;

  --shadow-sm: 0 2px 8px rgba(20,20,19,0.06);
  --shadow-md: 0 4px 14px rgba(20,20,19,0.08);
  --shadow-lg: 0 10px 30px rgba(20,20,19,0.10);
}
```

## Dark Mode Overrides

```css
html.dark {
  --ivory: #141413;
  --slate: #FAF9F5;
  --oat: #2A2A27;
  --gray-150: #2A2A27;
  --gray-300: #3D3D3A;
  --gray-500: #87867F;
  --gray-700: #D1CFC5;
  --white: #1E1E1C;
}
```

## Apply-Before-Paint Script

```html
<script>
(function(){
  var theme = localStorage.getItem('theme');
  if (theme === 'dark' || (!theme && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
    document.documentElement.classList.add('dark');
  }
})();
</script>
```

## Theme Toggle Button

```html
<button id="theme-toggle" aria-label="Toggle dark mode" style="
  position:fixed; top:16px; right:16px; z-index:100;
  width:36px; height:36px; border-radius:50%;
  border:1.5px solid var(--gray-300); background:var(--white);
  cursor:pointer; display:flex; align-items:center; justify-content:center;
  font-size:16px;
"></button>
```

```js
var toggle = document.getElementById('theme-toggle');
toggle.addEventListener('click', function(){
  var html = document.documentElement;
  html.classList.toggle('dark');
  localStorage.setItem('theme', html.classList.contains('dark') ? 'dark' : 'light');
});
```

## Token Usage Rules

1. **Colors**: always reference CSS variables, never hard-coded hex.
2. **SVG**: class-based coloring (`.st`, `.fl`, `.cl`, `.ol`, `.oa`, `.sl`) — never inline hex.
3. **Fonts**: `var(--serif)` for headings, `var(--sans)` for body, `var(--mono)` for labels/eyebrows/code.
4. **Radius**: use token sizes, not arbitrary px values.
5. **Shadows**: pick from the 3-level shadow system, don't invent new values.

## Palette Roles

| Token | Role |
|-------|------|
| `--ivory` | Page background |
| `--slate` | Primary text |
| `--clay` | Accent, CTAs, highlights, links |
| `--oat` | Secondary surfaces, card backgrounds |
| `--olive` | Success, positive deltas, secondary accent |
| `--gray-150` | Subtle backgrounds |
| `--gray-300` | Borders, dividers |
| `--gray-500` | Muted text, metadata |
| `--gray-700` | Secondary text, subtitles |
| `--white` | Card surfaces |
