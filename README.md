# Zotero → Obsidian literature note template (YAML frontmatter)

An updated [Zotero Integration](https://github.com/mgmeyers/obsidian-zotero-integration)
template for Obsidian, rewritten to use **YAML frontmatter** so it works with
Obsidian's Properties system.

This is a drop-in replacement for the template in Alexandra Phelan's guide
[*An Updated Academic Workflow: Zotero & Obsidian*](https://medium.com/@alexandraphelan/an-updated-academic-workflow-zotero-obsidian-cffef080addd),
which predates Obsidian Properties. The original used inline Dataview-style
fields in the note body; those keys now live in frontmatter, where Obsidian can
index, filter, and display them natively.

## What it does

Running the Zotero Integration command creates a literature note with:

- **Frontmatter:** `title`, `year`, `importdate`, `category: literaturenote`,
  `citekey`, `authors` (as wikilinks), `tags` (normalised to lowercase with
  underscores, brackets stripped)
- **Links** to attached PDFs (with URL-encoded paths)
- **Abstract** and any **Zotero notes**
- **Annotations** as callouts, split into highlights and notes, inside a
  `{% persist %}` block so re-running the import appends new annotations without
  overwriting your own writing

## Installation

1. Install the **Zotero Integration** community plugin in Obsidian.
2. Save `literature-note.md` from this repo into your vault (a `templates/`
   folder works well).
3. In the plugin settings, add a **Citation Format** of type `Create/Update Note`
   and point *Template File* at that file.
4. Set the output folder, then run the command from Zotero or the Obsidian
   command palette.

## Notes

- Requires Obsidian 1.4+ for Properties support.
- Author names are wrapped in wikilinks, so each author becomes a note you can
  use as a hub. Delete the brackets in the template if you don't want that.
- Tag normalisation is opinionated. Adjust the `replace` filters to taste.
- Zotero Integration runs [Nunjucks](https://mozilla.github.io/nunjucks/templating.html) with trimBlocks enabled, which removes the newline immediately after any {% %} tag. Every block tag in this template therefore sits alone on its own line, where it disappears cleanly. If you put a tag at the end of a content line, that line loses its break: the frontmatter collapses onto one line and Obsidian stops parsing it as Properties. Keep one tag per line, and put separating blank lines inside conditional blocks rather than around them.
- Test on a fresh item, or delete the note first.

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

Original workflow and template by Alexandra Phelan
([article](https://medium.com/@alexandraphelan/an-updated-academic-workflow-zotero-obsidian-cffef080addd)).
This repo only updates it for YAML frontmatter.

## License

MIT
