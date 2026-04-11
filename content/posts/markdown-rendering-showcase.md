---
title: "Markdown Rendering Showcase"
date: 2026-04-10T20:30:00+05:30
slug: "markdown-rendering-showcase"
description: "A validation page for headings, lists, tables, code blocks, blockquotes, footnotes, and embedded HTML."
cover: "https://images.unsplash.com/photo-1469474968028-56623f02e42e?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["markdown", "hugo", "theme", "testing"]
---

Use this page to validate how the theme renders common Markdown blocks.

## Headings and Paragraphs

This paragraph has **bold text**, *italic text*, and inline `code`.

### Mixed Inline Elements

Visit [Hugo docs](https://gohugo.io/documentation/) for syntax references.

## Lists

Unordered:

- First item
- Second item
  - Nested item
- Third item

Ordered:

1. Step one
2. Step two
3. Step three

Task list:

- [x] Add theme styles
- [x] Add long-form posts
- [ ] Add search page later

## Quote and Rule

> Containers are simple until production traffic arrives.

---

## Code Blocks

```bash
docker build -t app:dev .
docker run --rm -p 8080:8080 app:dev
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 2
```

```go
package main

import "fmt"

func main() {
    fmt.Println("hello from markdown showcase")
}
```

## Table

| Feature | Status | Notes |
| --- | --- | --- |
| Code fences | OK | Styled block with scroll |
| Tables | OK | Responsive horizontal overflow |
| Footnotes | OK | Linked note at bottom |

## Embedded HTML

<details>
  <summary>Expandable section</summary>
  <p>This is raw HTML inside markdown content.</p>
</details>

## Footnote Example

Markdown footnotes are useful for side notes.[^one]

[^one]: This is a rendered footnote used for theme validation.
