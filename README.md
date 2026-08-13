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
  `citekey`, `author` (as wikilinks), `tags` (normalised to lowercase with
  underscores, brackets stripped)
- **Metadata block:** journal / book, volume, issue, publisher, place, pages,
  DOI, ISBN, item type
- **Links** to attached PDFs (with URL-encoded paths)
- **Abstract** and any **Zotero notes**
- **Annotations** as callouts, split into highlights and notes, inside a
  `{% persist %}` block so re-running the import appends new annotations without
  overwriting your own writing

## Installation

1. Install the **Zotero Integration** community plugin in Obsidian.
2. Save `LN_templates.md` from this repo into your vault (a `templates/`
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
- The template uses [Nunjucks](https://mozilla.github.io/nunjucks/templating.html)
  syntax, same as the plugin's own docs.

## Credit

Original workflow and template by Alexandra Phelan
([article](https://medium.com/@alexandraphelan/an-updated-academic-workflow-zotero-obsidian-cffef080addd)).
This repo only updates it for YAML frontmatter.

## License

MIT
