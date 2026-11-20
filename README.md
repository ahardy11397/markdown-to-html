# markdown-to-html

A robust, graduate-level Markdown-to-HTML converter CLI written entirely in [Memelang](https://github.com/memelang/meme).

The program compiles under Memelang's offline `--provider heuristic` and demonstrates deterministic, cross-target parity across all six compiler backends: **Python**, **Rust**, **C++20**, **Go**, **C (C11)**, and **TypeScript (Node.js)**.

---

## Supported Syntax

| Category | Markdown Syntax | HTML Output | Description |
|---|---|---|---|
| **Headings** | `# Heading 1` .. `###### Heading 6` | `<h1>` .. `<h6>` | ATX headings with 1 to 6 `#` followed by a space. Trailing content is trimmed and receives inline markup. |
| **Code Blocks** | ```` ```lang ... ``` ```` | `<pre><code>...</code></pre>` | Fenced code blocks; language tag is ignored; inner content has `&`, `<`, `>` escaped. No inline markup is processed. |
| **Unordered Lists** | `- item` | `<ul><li>..</li></ul>` | Consecutive `- ` lines are grouped into a single `<ul>`. Nested lists not required. |
| **Ordered Lists** | `1. item` (or `N. item`) | `<ol><li>..</li></ol>` | Consecutive digit(s) followed by `. ` lines are grouped into a single `<ol>`. |
| **Blockquotes** | `> line` | `<blockquote>..</blockquote>` | Consecutive blockquote lines are grouped into one `<blockquote>` with `<br />` between lines. |
| **Horizontal Rules** | `---` (3+ dashes) | `<hr />` | Three or more consecutive dashes on their own line. |
| **Paragraphs** | `line1\nline2` | `<p>line1 line2</p>` | Consecutive non-blank lines join into one `<p>` with a single space between joined lines. Blank lines separate paragraphs. |
| **Bold** | `**bold**` | `<strong>bold</strong>` | Double asterisk delimiter; supports nested inline markup. |
| **Italic** | `*italic*` | `<em>italic</em>` | Single asterisk delimiter; supports nested inline markup. |
| **Inline Code** | `` `code` `` | `<code>code</code>` | Backtick delimiter; angle brackets and ampersands are escaped; internal markup is inert. |
| **Links** | `[text](url)` | `<a href="url">text</a>` | Anchor links; `text` receives inline markup; `url` is preserved. |

---

## Escaping Rules

1. **Regular Text:**
   All `&`, `<`, and `>` characters are escaped to `&amp;`, `&lt;`, and `&gt;` **before** inline markup is applied.
   - Any raw HTML tag (e.g. `<script>alert(1)</script>`) is safely neutralized to `&lt;script&gt;alert(1)&lt;/script&gt;`, rendering HTML injection attempts completely inert.
2. **Code Spans & Fences:**
   All code content preserves angle brackets and ampersands safely escaped (`&amp;`, `&lt;`, `&gt;`). No inline markup is parsed inside code fences or code spans.
3. **Escaping Precedence:**
   Ampersands (`&`) are always escaped first to `&amp;`, followed by `<` to `&lt;` and `>` to `&gt;`, avoiding double-escaping.

---

## CLI Usage

The CLI is a thin argv-router implemented in `main.meme`.

### Build Native Executable

Build the standalone native binary via the C11 backend:

```bash
uv run --project /mnt/ssd/projects/meme meme build main.meme -o md_bin
```

### Commands

```bash
# Render markdown file to HTML on stdout
./md_bin render <file.md>

# Render markdown file to output HTML file
./md_bin render-file <in.md> <out.html>

# Debug token stream (one line per block: `heading <level>`, `list-item`, `paragraph`, `code`, `blockquote`, `hr`)
./md_bin tokens <file.md>
```

### Error Handling

- Missing input files are read with Memelang's optional file reader (`read string from file <path> or else nothing`).
- When a file is missing or unreadable, the program outputs a single stderr-style error line:
  ```text
  Error: file not found: <path>
  ```
  and terminates immediately with exit code `1` (zero traceback).
- Invalid arguments or unknown commands output a usage message and exit with code `1`.

---

## Architecture

The project is structured into modular, single-responsibility Memelang source files:

- `md_inline.meme`: Character-level inline scanner, HTML escaping (`&`, `<`, `>`), and recursive markup rendering (`code`, `links`, `bold`, `italic`).
- `md_block.meme`: Line-oriented block scanner, ATX heading detector, code fence tracker, list accumulator, blockquote aggregator, and paragraph coalescer.
- `md_render.meme`: Block-to-HTML serializer, token stream extractor (`heading 1`, `list-item`, `paragraph`, `code`, `blockquote`, `hr`), and full document renderer.
- `main.meme`: CLI argv dispatcher and optional file I/O coordinator.
- `tests/meme/*.meme`: Test suites organized into isolated unit and integration specs.

---

## Running Tests

Run the test suite across all six supported compiler backends using `meme test`:

```bash
# Python
uv run --project /mnt/ssd/projects/meme meme test tests/meme --target python

# Rust
uv run --project /mnt/ssd/projects/meme meme test tests/meme --target rust

# C++20
uv run --project /mnt/ssd/projects/meme meme test tests/meme --target cpp

# Go
uv run --project /mnt/ssd/projects/meme meme test tests/meme --target go

# C11
uv run --project /mnt/ssd/projects/meme meme test tests/meme --target c

# TypeScript
uv run --project /mnt/ssd/projects/meme meme test tests/meme --target ts
```

All 31 test cases pass across all six targets with 0 failures.
