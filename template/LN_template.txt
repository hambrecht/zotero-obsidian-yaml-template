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
