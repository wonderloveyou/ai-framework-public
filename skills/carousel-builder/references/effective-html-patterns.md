# Effective-HTML Patterns for Carousels

Source: [plannotator/effective-html](https://github.com/plannotator/effective-html) — Apache 2.0 licensed corpus of self-contained HTML patterns.

## Design Tokens (dark-mode-ready palette)

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

  --serif: ui-serif, Georgia, "Times New Roman", serif;
  --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
}
```

## Dark Mode Recipe

```html
<head>
<script>
(function(){
  var theme = localStorage.getItem('theme');
  if (theme === 'dark' || (!theme && window.matchMedia('(prefers-color-scheme:dark)').matches)) {
    document.documentElement.classList.add('dark');
  }
})();
</script>
</head>
```

```css
html.dark {
  --ivory: #141413;
  --slate: #FAF9F5;
  --gray-150: #2A2A27;
  --gray-300: #3D3D3A;
  --gray-500: #87867F;
  --gray-700: #D1CFC5;
  --white: #1E1E1C;
}
```

Theme toggle: small button top-right, toggles `html.dark`, saves to `localStorage`.

SVG elements must use CSS variables, never hard-coded hex:
```css
.icon-stroke { stroke: var(--gray-500); fill: none; stroke-width: 2.5; }
.icon-fill   { fill: var(--gray-300); }
.icon-accent { fill: var(--clay); }
```

## Scroll-Snap Slide Deck Shell

```css
body {
  scroll-snap-type: y mandatory;
  overflow-x: hidden;
}
.slide {
  width: 100vw; height: 100vh;
  scroll-snap-align: start;
  scroll-snap-stop: always;
  display: flex; align-items: center; justify-content: center;
  padding: 8vh 6vw;
}
.slide-inner { width: 100%; max-width: 780px; }
.slide.invert { background: var(--slate); color: var(--ivory); }
```

```js
// Keyboard navigation
document.addEventListener('keydown', function(e){
  if (e.key === 'ArrowRight' || e.key === 'ArrowDown' || e.key === ' ') { e.preventDefault(); go(current+1); }
  if (e.key === 'ArrowLeft'  || e.key === 'ArrowUp')                    { e.preventDefault(); go(current-1); }
});
// Slide counter via IntersectionObserver
var obs = new IntersectionObserver(function(entries){
  entries.forEach(function(en){
    if (en.isIntersecting) { counter.textContent = (idx+1) + ' / ' + total; }
  });
}, { threshold: 0.6 });
```

## Typography Hierarchy

```css
.eyebrow {
  font-family: var(--mono);
  font-size: 12px;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--gray-500);
  margin-bottom: 28px;
}
h1 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: clamp(40px, 6vw, 64px);
  line-height: 1.08;
  letter-spacing: -0.01em;
  margin-bottom: 20px;
}
h2 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: clamp(30px, 4vw, 42px);
  line-height: 1.15;
  margin-bottom: 44px;
}
.subtitle {
  font-size: 17px;
  line-height: 1.6;
  color: var(--gray-700);
  max-width: 520px;
}
```

## Card Variant Matrix (6 treatments)

| Variant | Class | Use Case |
|---------|-------|----------|
| Flat | `.v-flat` | Dense lists on tinted backgrounds |
| Outlined | `.v-outlined` | Default content cards on ivory |
| Elevated | `.v-elevated` | Draggable items, popovers |
| Accent stripe | `.v-stripe` | Pinned or priority items (`::before` pseudo) |
| Inset/Filled | `.v-inset` | Nested cards inside white panels |
| Horizontal | `.v-horizontal` | Compact row lists, sidebars |

```css
.card {
  border-radius: 12px;
  padding: 20px;
  transition: padding 0.15s, box-shadow 0.15s, border 0.15s;
}
.card:hover { outline: 2px solid var(--clay); outline-offset: 2px; }

.v-flat     { background: var(--white); border: none; box-shadow: none; }
.v-outlined { background: var(--white); border: 1.5px solid var(--gray-300); }
.v-elevated { background: var(--white); box-shadow: 0 4px 14px rgba(20,20,19,0.08); }
.v-stripe   { position: relative; overflow: hidden; }
.v-stripe::before { content:""; position:absolute; left:0;right:0;top:0; height:4px; background:var(--clay); }
.v-inset    { background: var(--oat); border: none; box-shadow: none; }
.v-horizontal { display: flex; align-items: center; gap: 14px; }
```

## Slide Content Patterns

### Shipped List (bullet achievements)

```css
.ship-list { list-style:none; display:flex; flex-direction:column; gap:28px; }
.ship-item {
  display: grid;
  grid-template-columns: 14px 1fr auto;
  column-gap: 20px; align-items: baseline;
  padding-bottom: 24px;
  border-bottom: 1px solid var(--gray-150);
}
.ship-dot { width:9px; height:9px; border-radius:50%; background:var(--olive); }
.ship-title { font-family:var(--serif); font-size:20px; font-weight:500; }
```

### Progress Bars

```css
.prog-track { width:100%; height:5px; background:var(--gray-150); border-radius:3px; overflow:hidden; }
.prog-fill  { height:100%; background:var(--clay); border-radius:3px; }
.prog-head  { display:flex; justify-content:space-between; align-items:baseline; }
```

### Metrics Grid

```css
.metrics { display:grid; grid-template-columns:1fr 1fr; gap:56px; }
.metric-value { font-family:var(--serif); font-size:52px; font-weight:500; line-height:1; }
.metric-delta.down { color:var(--olive); }
.metric-delta.up   { color:var(--clay); }
```

### Decision Card (inverted slide)

```css
.decision-card {
  border: 1.5px solid var(--clay);
  border-radius: 14px;
  padding: 36px 38px;
  background: rgba(217,119,87,0.06);
}
.chip {
  font-family: var(--mono);
  font-size: 12px;
  padding: 10px 18px;
  border-radius: 999px;
  border: 1px solid var(--gray-700);
}
.chip.lean { border-color: var(--clay); color: var(--clay); }
```

### Next Week / On Deck

```css
.next-list { list-style:none; display:flex; flex-direction:column; gap:22px; }
.next-list li {
  font-family: var(--serif);
  font-size: 21px; line-height: 1.45;
  padding-left: 36px; position: relative;
}
.next-list li::before {
  content:""; position:absolute; left:0; top:0.55em;
  width:18px; height:1.5px; background:var(--clay);
}
```

## Responsive Breakpoints

```css
@media (max-width: 640px) {
  .metrics { grid-template-columns: 1fr; gap: 36px; }
  .ship-item { grid-template-columns: 14px 1fr; }
}
```
