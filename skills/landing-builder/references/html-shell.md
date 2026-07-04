# HTML Shell

Self-contained single-file HTML landing page shell.

## Complete Shell Template

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Page Title</title>
<meta name="description" content="Description for SEO and social previews.">

<script>
(function(){
  var theme = localStorage.getItem('theme');
  if (theme === 'dark' || (!theme && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
    document.documentElement.classList.add('dark');
  }
})();
</script>

<style>
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

  * { margin: 0; padding: 0; box-sizing: border-box; }

  html { scroll-behavior: smooth; }

  body {
    font-family: var(--sans);
    background: var(--ivory);
    color: var(--slate);
    line-height: 1.55;
    -webkit-font-smoothing: antialiased;
    min-height: 100vh;
  }

  .wrap { max-width: 1120px; margin: 0 auto; padding: 0 32px; }

  @media (max-width: 960px) {
    /* collapse 2-col to 1-col here */
  }
  @media (max-width: 640px) {
    .wrap { padding: 0 20px; }
    /* collapse 3-col to 1-col here */
  }
</style>
</head>
<body>

  <button id="theme-toggle" aria-label="Toggle dark mode" style="
    position:fixed; top:16px; right:16px; z-index:100;
    width:36px; height:36px; border-radius:50%;
    border:1.5px solid var(--gray-300); background:var(--white);
    cursor:pointer; display:flex; align-items:center; justify-content:center;
    font-size:16px; color:var(--slate);
  ">☀</button>

  <!-- SECTIONS GO HERE -->

<script>
(function(){
  var toggle = document.getElementById('theme-toggle');
  if (document.documentElement.classList.contains('dark')) {
    toggle.textContent = '☾';
  }
  toggle.addEventListener('click', function(){
    var html = document.documentElement;
    html.classList.toggle('dark');
    toggle.textContent = html.classList.contains('dark') ? '☾' : '☀';
    localStorage.setItem('theme', html.classList.contains('dark') ? 'dark' : 'light');
  });
})();
</script>

</body>
</html>
```

## Layout Rules

1. **Self-contained**: no external CSS, JS, or fonts. System font stack only.
2. **No frameworks**: hand-rolled CSS. No Tailwind, Bootstrap, or JS frameworks.
3. **Max width**: 1120px content wrapper, centered with `margin: 0 auto`.
4. **Section spacing**: `margin-top: 72px` between sections, first section gets `padding-top: 80px`.
5. **Responsive breakpoints**:
   - 960px: collapse 2-column layouts to 1 column
   - 640px: collapse 3+ column layouts to 1, reduce padding to 20px
6. **Text width**: body copy max 620-700px for readability.
7. **SVG coloring**: use CSS classes (`.st`, `.fl`, `.cl`, `.ol`, `.oa`, `.sl`), never inline hex.

## SVG Color Classes

```css
.thumb svg .st { stroke: var(--gray-500); fill: none; stroke-width: 2.5; }
.thumb svg .fl { fill: var(--gray-300); }
.thumb svg .cl { fill: var(--clay); }
.thumb svg .ol { fill: var(--olive); }
.thumb svg .oa { fill: var(--oat); stroke: var(--gray-500); stroke-width: 2.5; }
.thumb svg .sl { fill: var(--slate); }
.thumb svg .wh { fill: var(--paper); stroke: var(--gray-500); stroke-width: 2.5; }
.thumb svg .ln { stroke: var(--gray-500); stroke-width: 2.5; fill: none; stroke-linecap: round; }
.thumb svg .lc { stroke: var(--clay); stroke-width: 2.5; fill: none; stroke-linecap: round; }
.thumb svg .da { stroke-dasharray: 4 4; }
```

Dark mode automatically recolors SVGs because they reference CSS variables.
