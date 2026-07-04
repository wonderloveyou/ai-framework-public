# Section Patterns

Source: [plannotator/effective-html](https://github.com/plannotator/effective-html) index.html, 14-research-feature-explainer.html, 02-exploration-visual-designs.html, 05-design-system.html.

## Hero Section

```html
<header class="hero">
  <div class="hero-grid">
    <div class="hero-text">
      <div class="eyebrow">Category or context</div>
      <h1>Fluid heading with <em>emphasis</em></h1>
      <p class="hero-subtitle">
        One or two sentences that explain the value. Max 620px width for readability.
      </p>
      <div class="hero-actions">
        <a href="#cta" class="btn-primary">Primary CTA</a>
        <a href="#more" class="btn-ghost">Secondary</a>
      </div>
    </div>
    <div class="hero-visual">
      <!-- SVG illustration, screenshot, or abstract geometry -->
    </div>
  </div>
</header>
```

```css
.hero {
  padding: 80px 0 56px;
  border-bottom: 1.5px solid var(--gray-300);
}
.hero-grid {
  display: grid;
  grid-template-columns: 1fr 340px;
  gap: 48px;
  align-items: end;
}
@media (max-width: 880px) { .hero-grid { grid-template-columns: 1fr; } }

.eyebrow {
  font-family: var(--mono);
  font-size: 12px;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--gray-500);
  margin-bottom: 18px;
  display: flex;
  align-items: center;
  gap: 12px;
}
.eyebrow::before {
  content: "";
  width: 24px; height: 1.5px;
  background: var(--clay);
}

h1 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: clamp(38px, 5.4vw, 62px);
  line-height: 1.06;
  letter-spacing: -0.018em;
  margin: 0 0 8px;
}
h1 em { font-style: italic; color: var(--clay); }

.hero-subtitle {
  font-size: 16.5px;
  color: var(--gray-700);
  max-width: 620px;
  margin: 22px 0 0;
}

.hero-actions {
  display: flex;
  gap: 12px;
  margin-top: 32px;
}
```

## Button Patterns

```css
.btn-primary {
  display: inline-flex;
  align-items: center;
  height: 44px;
  padding: 0 24px;
  font-family: var(--sans);
  font-size: 15px;
  font-weight: 500;
  color: var(--white);
  background: var(--clay);
  border: none;
  border-radius: var(--radius-md);
  cursor: pointer;
  text-decoration: none;
  transition: background 150ms;
}
.btn-primary:hover { background: #C56540; }

.btn-ghost {
  display: inline-flex;
  align-items: center;
  height: 44px;
  padding: 0 24px;
  font-family: var(--sans);
  font-size: 15px;
  font-weight: 500;
  color: var(--gray-700);
  background: transparent;
  border: 1.5px solid var(--gray-300);
  border-radius: var(--radius-md);
  cursor: pointer;
  text-decoration: none;
  transition: background 150ms, border-color 150ms;
}
.btn-ghost:hover { background: var(--gray-150); border-color: var(--slate); }
```

## Feature Grid

```html
<section class="section" id="features">
  <div class="sec-head">
    <h2>Features</h2>
    <p class="sec-subtitle">What you get</p>
  </div>
  <div class="feature-grid">
    <a class="feature-card" href="#detail">
      <div class="card-thumb"><!-- SVG --></div>
      <div class="card-body">
        <div class="card-title">Feature name</div>
        <div class="card-desc">Short description of the feature and its benefit.</div>
      </div>
    </a>
    <!-- repeat for 3-6 cards -->
  </div>
</section>
```

```css
.section {
  max-width: 1120px;
  margin: 0 auto;
  padding: 72px 32px 0;
}
.sec-head {
  margin-bottom: 36px;
}
.sec-head h2 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: 27px;
  letter-spacing: -0.012em;
  margin: 0;
}
.sec-subtitle {
  font-size: 14.5px;
  color: var(--gray-700);
  max-width: 700px;
  margin: 8px 0 0;
}

.feature-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(316px, 1fr));
  gap: 20px;
}
@media (max-width: 640px) { .feature-grid { grid-template-columns: 1fr; } }

.feature-card {
  display: flex;
  flex-direction: column;
  background: var(--white);
  border: 1.5px solid var(--gray-300);
  border-radius: var(--radius-lg);
  text-decoration: none;
  color: inherit;
  transition: transform 150ms ease, box-shadow 150ms ease, border-color 150ms ease;
  overflow: hidden;
}
.feature-card:hover {
  transform: translateY(-3px);
  box-shadow: var(--shadow-lg);
  border-color: var(--slate);
}

.card-thumb {
  height: 132px;
  background: var(--gray-150);
  border-bottom: 1.5px solid var(--gray-300);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 16px;
  transition: background 150ms;
}
.feature-card:hover .card-thumb { background: var(--oat); }

.card-body { padding: 18px 20px 16px; }
.card-title {
  font-family: var(--serif);
  font-size: 19px;
  font-weight: 500;
  line-height: 1.22;
  margin-bottom: 7px;
}
.card-desc {
  font-size: 13.5px;
  color: var(--gray-700);
  line-height: 1.5;
}
```

## Collapsible Details (FAQ / Steps)

```html
<details class="faq-item">
  <summary>What is this and why should I care?</summary>
  <div class="faq-answer">
    <p>Answer text here. Can include code, lists, or links.</p>
  </div>
</details>
```

```css
.faq-item {
  border-bottom: 1px solid var(--gray-150);
  padding: 20px 0;
}
.faq-item summary {
  font-family: var(--serif);
  font-size: 19px;
  font-weight: 500;
  cursor: pointer;
  list-style: none;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.faq-item summary::-webkit-details-marker { display: none; }
.faq-item summary::after {
  content: "+";
  font-family: var(--mono);
  font-size: 18px;
  color: var(--gray-500);
  transition: transform 150ms;
}
.faq-item[open] summary::after {
  content: "−";
}
.faq-answer {
  margin-top: 14px;
  font-size: 15px;
  line-height: 1.6;
  color: var(--gray-700);
}
```

## CTA Block

```html
<section class="cta-block">
  <div class="cta-inner">
    <h2>Ready to start?</h2>
    <p>One sentence that removes the last objection.</p>
    <div class="cta-actions">
      <a href="#" class="btn-primary">Get started</a>
      <a href="#" class="btn-ghost">Learn more</a>
    </div>
  </div>
</section>
```

```css
.cta-block {
  margin-top: 80px;
  padding: 80px 32px;
  background: var(--oat);
  text-align: center;
}
.cta-block h2 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: clamp(30px, 4vw, 42px);
  margin: 0 0 12px;
}
.cta-block p {
  font-size: 17px;
  color: var(--gray-700);
  margin: 0 0 32px;
}
.cta-actions {
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
}
```

## Metrics Grid

```css
.metrics {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 48px;
}
.metric-label {
  font-family: var(--mono);
  font-size: 11px;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--gray-500);
  margin-bottom: 14px;
}
.metric-value {
  font-family: var(--serif);
  font-size: 52px;
  font-weight: 500;
  line-height: 1;
}
.metric-delta {
  font-family: var(--mono);
  font-size: 13px;
  margin-top: 12px;
}
.metric-delta.down { color: var(--olive); }
.metric-delta.up   { color: var(--clay); }
```

## Empty States (from 02-exploration-visual-designs.html)

| Variant | When |
|---------|------|
| Minimal | Typography-only, clean and quiet |
| Illustrated | Inline SVG + heading + CTA button |
| Playful | CSS animation, bobbing cards, for lighter brand voice |
| Instructional | Numbered steps, onboarding flow |

## Footer

```html
<footer>
  <span class="footer-brand">Brand name</span>
  <nav class="footer-links">
    <a href="#">Link</a>
    <a href="#">Link</a>
    <a href="#">Link</a>
  </nav>
  <span class="footer-copy">&copy; 2026</span>
</footer>
```

```css
footer {
  max-width: 1120px;
  margin: 100px auto 0;
  padding: 36px 32px 60px;
  border-top: 1.5px solid var(--gray-300);
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  gap: 20px;
  flex-wrap: wrap;
  font-size: 13px;
  color: var(--gray-500);
}
.footer-brand { font-family: var(--serif); font-style: italic; color: var(--gray-700); font-size: 15px; }
.footer-links { display: flex; gap: 20px; }
.footer-links a { color: var(--gray-500); text-decoration: none; }
.footer-links a:hover { color: var(--clay); }
```
