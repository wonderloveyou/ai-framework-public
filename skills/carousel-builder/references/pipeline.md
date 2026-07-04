# Carousel Pipeline

Use this sequence for most carousel work:

1. **Input intake**
   - topic, article, draft, CSV, or existing HTML
   - platform and format
   - audience and tone
   - brand/profile
2. **Structure**
   - define slide count
   - assign narrative role per slide
   - write short copy
   - mark deletable facts
3. **Visual brief**
   - one visual object per slide
   - palette roles
   - typography roles
   - layout/composition
   - image/icon/screenshot needs
4. **Render**
   - HTML/CSS artboards, image prompts, or template engine
   - platform-specific dimensions
   - export filenames
5. **Brand/style QA**
   - contrast, typography, spacing, density
   - brand colors and voice
   - mobile readability
6. **Roast pass**
   - remove duplication
   - improve weak hooks
   - make final slide non-duplicative
   - delete decorative clutter
7. **Package**
   - PNG/JPG/WebP exports
   - ZIP if multi-slide
   - source HTML/JSON
   - update reusable references if a pattern was learned

## Architecture Pattern

For automation, split responsibilities:

```text
text/article/CSV
  -> structure LLM
  -> visual brief LLM
  -> render engine or image model
  -> brand/style validator
  -> export/uploader
```

This maps cleanly to n8n, MCP tools, scripts, or manual Codex workflows.

## Reference Intake

Use when the user provides examples, folders, screenshots, palettes, or competitor carousels.

Analyze:

- format and aspect ratio
- palette roles
- typography roles
- composition patterns
- density level
- slide rhythm
- CTA style
- repeated visual devices
- what feels brand-specific vs reusable

Extract observations into:

- reusable global pattern
- brand/profile-specific pattern
- anti-pattern
- template candidate
- palette candidate
- export/technical lesson

For large image folders, create and inspect contact sheets. Do not summarize only filenames.

## Batch Generation

Use for CSV-to-images, content factories, and bulk card generation.

Minimum CSV fields:

```csv
id,platform,width,height,topic,headline,body,cta,brand_profile,output_name
```

Useful extra fields:

```csv
tone,audience,visual_object,accent_color,logo_path,source_url,priority,variant
```

Pipeline:

```text
CSV row
  -> validate required fields
  -> structure/brief generation
  -> brand token injection
  -> render/image prompt
  -> QA checks
  -> export file
  -> manifest
```

For n8n: Read CSV/Airtable/Notion -> LLM brief -> brand/style node -> render/image API -> storage -> notification.
