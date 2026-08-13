# Zotero → Obsidian literature note template (YAML frontmatter)

An updated [Zotero Integration](https://github.com/community-archive/obsidian-zotero-integration)
literature note template for Obsidian, rewritten to use **YAML frontmatter** so it
works with Obsidian's Properties system.

This is a drop-in replacement for the template in Alexandra Phelan's guide
[*An Updated Academic Workflow: Zotero & Obsidian*](https://medium.com/@alexandraphelan/an-updated-academic-workflow-zotero-obsidian-cffef080addd),
which predates Obsidian Properties. The original stored bibliographic metadata as
inline Dataview-style fields in the note body. Those keys now live in frontmatter,
where Obsidian can index, filter, sort and display them natively, and where Bases
and Dataview can query them without custom parsing.

![A generated literature note in Obsidian, showing bibliographic metadata in the
Properties panel and an imported Zotero highlight rendered as a quote callout](docs/example.png)


## Requirements

- Obsidian 1.4 or later, for Properties support.
- The **Zotero Integration** community plugin, reasonably up to date. The
  per-annotation page links use `a.desktopURI` and image annotations use
  `a.imageRelativePath`; on older plugin releases these variables don't exist and
  those lines render empty rather than erroring.
- Zotero 7 with the PDF reader, if you want annotation import.

## Installation

1. Install the **Zotero Integration** plugin in Obsidian.
2. Save `literature-note.md` from this repo into your vault, for example in a
   `templates/` folder. You can also copy the template out of the code block at
   the bottom of this README.
3. In the plugin settings, add a **Citation Format** with format
   `Create/Update Note` and point *Template File* at that file.
4. Set the output folder, then run the command from Zotero or from the Obsidian
   command palette.

## What it produces

**Frontmatter**

`title`, `citekey`, `year`, `authors` (a YAML list of wikilinks, so every author
becomes a hub note), `itemType`, `publication` (journal or containing book),
`volume`, `issue`, `pages`, `publisher`, `place`, `DOI`, `ISBN`,
`category: literaturenote`, `importdate`, and `tags` normalised to lowercase with
underscores and brackets stripped. Fields absent from the Zotero item are omitted
rather than left blank.

**Body**

- The formatted bibliography in a `> [!cite]` callout.
- Links to attached PDFs, with URL-encoded file paths.
- The abstract, and any notes written in Zotero.
- PDF annotations: highlights as `> [!quote]` callouts, comments as `> [!note]`,
  image annotations embedded, each with a `zotero://` link back to its page in the
  desktop app. The whole annotation section sits inside a `{% persist %}` block, so
  re-importing an item appends newly added highlights without touching the
  synthesis you have written around them.

## Example output

```
---
title: "Understanding the Effect of Forest Recovery Parameterization on Modeled Streamflow"
citekey: "bouffordUnderstandingEffectForest2026"
year: 2026
authors: ["[[Boufford, Brianne L.]]", "[[Coops, Nicholas C.]]"]
itemType: journalArticle
publication: "Canadian Journal of Remote Sensing"
volume: "52"
issue: "1"
pages: "2652004"
DOI: "10.1080/07038992.2026.2652004"
category: literaturenote
importdate: 2026-08-13
tags: ["remote_sensing", "hydrology"]
---

# Understanding the Effect of Forest Recovery Parameterization on Modeled Streamflow

> [!cite]
> Boufford, B. L., & Coops, N. C. (2026). Understanding the Effect…

## Files
- [PDF](file://…)

## Abstract
Characterizing the hydrological effects of forest disturbance and recovery…

## Annotations
### Imported 2026-08-13 8:23 am

> [!quote]
> After 10 years, LAI recovery ranged from 45% to 73% across ecozones.
>
> [p. 1](zotero://open-pdf/…)
```

## Editing this template

Zotero Integration runs [Nunjucks](https://mozilla.github.io/nunjucks/templating.html)
with `trimBlocks` enabled, which removes the newline immediately following any
`{% %}` tag. This has one practical consequence that governs the whole template:

**Every block tag sits alone on its own line.** A tag on its own line disappears
cleanly, tag and newline together, leaving the surrounding line breaks intact. A
tag placed at the *end* of a content line eats that line's break instead. Do that
inside the frontmatter and the whole block collapses onto a single line, at which
point Obsidian stops recognising it as Properties and renders it as body text.

The corollary is that separating blank lines belong *inside* conditional blocks,
immediately after the `{% if %}`, not around them. A blank line placed outside the
block survives even when the block itself produces nothing, so sections that don't
apply to an item leave their padding behind and the note fills with gaps.

If you edit the template and the note comes out on one line or full of whitespace,
this is almost always the cause.

## Notes and caveats

- **Testing changes.** Annotations live inside `{% persist %}`, which preserves
  whatever is already between the persist markers. Re-importing an item that
  already has a note will *not* reformat its existing annotations, so template
  changes appear to have no effect. Test against an item you haven't imported
  before, or delete the note first.
- **GitHub's preview of `literature-note.md` is broken.** GitHub tries to parse the
  Nunjucks frontmatter as real YAML, fails, and shows an "Error in user YAML"
  banner above a mangled preview. This is expected and says nothing about the
  template. Use the Raw view, or the code block below.
- **Author wikilinks.** Remove the `[[` and `]]` from the `authors` line if you
  don't want a note per author.
- **Tag normalisation is opinionated.** Adjust or drop the `replace` filters.
- **`year` is unquoted**, so Obsidian types it as a number. That is usually what
  you want for sorting; quote it if you'd rather have text.
- **Titles containing a backslash** will break the double-quoted YAML scalar, since
  YAML treats `\` as an escape character there. Double quotes in titles are already
  handled by a `replace` filter. Backslashes are rare enough that they aren't.

## Template

```
---
title: "{{title | replace('"', "'")}}"
citekey: "{{citekey}}"
{% if date %}
year: {{date | format("YYYY")}}
{% endif %}
authors: [{% for creator in creators %}"[[{% if creator.name %}{{creator.name}}{% else %}{{creator.lastName}}, {{creator.firstName}}{% endif %}]]"{% if not loop.last %}, {% endif %}{% endfor %}]
itemType: {{itemType}}
{% if publicationTitle %}
publication: "{{publicationTitle}}"
{% endif %}
{% if volume %}
volume: "{{volume}}"
{% endif %}
{% if issue %}
issue: "{{issue}}"
{% endif %}
{% if pages %}
pages: "{{pages}}"
{% endif %}
{% if publisher %}
publisher: "{{publisher}}"
{% endif %}
{% if place %}
place: "{{place}}"
{% endif %}
{% if DOI %}
DOI: "{{DOI}}"
{% endif %}
{% if ISBN %}
ISBN: "{{ISBN}}"
{% endif %}
category: literaturenote
importdate: {{importDate | format("YYYY-MM-DD")}}
tags: [{% for t in tags %}"{{t.tag | replace('"', '') | replace('[', '') | replace(']', '') | replace(' ', '_') | lower}}"{% if not loop.last %}, {% endif %}{% endfor %}]
---

# {{title}}

> [!cite]
> {{bibliography}}
{% if attachments | filterby("path", "endswith", ".pdf") | length %}

## Files
{% for attachment in attachments | filterby("path", "endswith", ".pdf") %}
- [{{attachment.title}}](file://{{attachment.path | replace(" ", "%20")}})
{% endfor %}
{% endif %}
{% if abstractNote %}

## Abstract
{{abstractNote}}
{% endif %}
{% if markdownNotes %}

## Notes
{{markdownNotes}}
{% endif %}

## Annotations
{% persist "annotations" %}
{% set newAnnotations = annotations | filterby("date", "dateafter", lastImportDate) %}
{% if newAnnotations.length %}
### Imported {{importDate | format("YYYY-MM-DD h:mm a")}}
{% for a in newAnnotations %}
{% if a.type == "highlight" %}
> [!quote]
{% endif %}
{% if a.type == "text" or a.type == "note" %}
> [!note]
{% endif %}
{% if a.type == "image" %}
> [!example] Figure
{% endif %}
{% if a.annotatedText %}
> {{a.annotatedText | replace("\n", "\n> ")}}
{% endif %}
{% if a.imageRelativePath %}
> ![[{{a.imageRelativePath}}]]
{% endif %}
{% if a.comment %}
>
> {{a.comment | replace("\n", "\n> ")}}
{% endif %}
{% if a.pageLabel %}
>
> [p. {{a.pageLabel}}]({{a.desktopURI}})
{% endif %}

{% endfor %}
{% endif %}
{% endpersist %}
```

## Credit

The original workflow and template are by Alexandra Phelan
([article](https://medium.com/@alexandraphelan/an-updated-academic-workflow-zotero-obsidian-cffef080addd)).
This repo only updates it for YAML frontmatter and Obsidian Properties.

## License

MIT. See [LICENSE](LICENSE).
