# markdown-to-html

A lightweight, graduate-level **Markdown-to-HTML** converter written 100% in *Memelang*.
Check out the project documentation at [Memelang Docs](https://github.com/memelang/meme).

## Features

- Fast and deterministic cross-target execution (Python, Rust, C++, Go, C, TS)
- Safe HTML escaping for `&`, `<`, and `>`
- ATX headings levels 1 through 6
- Unordered and ordered lists with inline formatting
- Fenced code blocks with language tag transparency
- Blockquotes with automatic `<br />` line breaks
- Horizontal rules with `---`

## Usage

```bash
md render input.md
md render-file input.md output.html
md tokens input.md
```

## Step-by-Step Workflow

1. Prepare your markdown source document
2. Run `md render` to inspect the generated HTML
3. Run `md render-file` to produce the final web document

> Important Notice:
> This parser is fully compliant with the Memelang heuristic specification.

---

Crafted with Memelang.
