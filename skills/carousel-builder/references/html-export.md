# HTML Artboards And Export

## Artboard Rules

- Build at native size.
- Scale only the preview wrapper.
- Export the native card, not the scaled preview.
- Use explicit width, height, and background.
- Start from a known template when possible:
  - generic refs: `W:\0SMM\service-files\refi\!CAROUSEL_TEMPLATE.html`
  - generic TG cover refs: `W:\0SMM\service-files\refi\!TG_COVER_TEMPLATE.html`
  - Velmi reference implementation: `W:\0SMM\projects\velmi-ai\habr-statia-crm\article_images.html`

```css
* { box-sizing: border-box; }
.card {
  position: relative;
  overflow: hidden;
  width: 1080px;
  height: 1350px;
  background: var(--bg);
}
.content-zone {
  position: absolute;
  inset: 96px 72px 88px;
  min-width: 0;
}
```

## Layout Guardrails

- `min-width: 0` on flex/grid text children.
- `flex-shrink: 0` on icons, badges, markers, and numbers.
- Fixed topbar/footer zones.
- Stable dimensions for repeated cards.
- Avoid relying on clipping to hide broken layout.
- Profiles should override CSS variables first. Do not fork layout CSS unless the format requires it.
- Use `text-overflow: ellipsis` only for labels, chips, and metadata. Do not ellipsize core meaning.
- Prefer responsive reduction by content priority: hide/de-emphasize support copy before shrinking the headline.

## Export Checklist

- card count equals filename count
- download buttons match cards
- ZIP export includes all cards
- UI controls hidden before rendering
- fonts are loaded before rendering
- clone/export stage has explicit background
- output dimensions match target format
- fixed layout classes remain intact when applying brand profile variables

## Debug Commands

```powershell
$html = Get-Content -Raw -Encoding UTF8 -LiteralPath '<file>'
([regex]::Matches($html,'<div(\s|>)')).Count
([regex]::Matches($html,'</div>')).Count
([regex]::Matches($html,'class="card"')).Count
```
