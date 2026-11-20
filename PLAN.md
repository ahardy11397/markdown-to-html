# PLAN — markdown-to-html: a Memelang graduate-level demo

Build a markdown-to-HTML converter CLI entirely in Memelang. Beta
demonstration program: the classic parsing/string-processing assignment.

Language docs: /mnt/ssd/projects/meme/docs/WRITING_GUIDE.md and
docs/TUTORIAL.md (read both first). Compiler commands via
`uv run --project /mnt/ssd/projects/meme meme ...` from this repo.

## Ground rules (non-negotiable)

- Work ONLY in this repo. NO git commits.
- 100% Memelang. Read the input file with the Optional reader
  (`read string from file <path> or else nothing`); missing file = one
  clean error line + exit 1, never a traceback.
- Everything must parse with `--provider heuristic`.
- Test suites run in the FOREGROUND only.

## Scope (exactly this subset; anything outside = literal text)

Blocks:
- `# ..` .. `###### ..` ATX headings → <h1>..<h6>
- ``` fenced code blocks → <pre><code>..</code></pre> (content escaped,
  language tag ignored)
- `- ` unordered list items → <ul><li>..</li></ul> (consecutive items
  grouped; nested lists NOT required)
- `1. ` ordered list items → <ol><li>..</li></ol>
- `> ` blockquote lines → <blockquote>..</blockquote> (consecutive lines
  grouped into one blockquote, <br /> between lines)
- `---` (3+ dashes) → <hr />
- blank line separates paragraphs; consecutive non-blank lines join into
  ONE <p> with a space between joined lines
Inline (applied to headings, list items, blockquotes, paragraphs):
- `**bold**` → <strong>, `*italic*` → <em>, `` `code` `` → <code>
- `[text](url)` → <a href="url">text</a>
- HTML-escape & < > in regular text BEFORE inline markup is applied; code
  spans/fences escape everything.

## CLI (thin argv-router main + logic modules; unknown command → usage +
exit 1; every error = one stderr-style line + exit 1)

    md render <file.md>          # HTML to stdout
    md render-file <in.md> <out.html>
    md tokens <file.md>          # debug: one line per block: `heading 1`,
                                 # `list-item`, `paragraph`, `code`, etc.

## Tests — tests/meme/*.meme, `meme test`

At minimum: each block type's exact expected HTML (hand-written expected
strings, assert equality); two consecutive paragraphs; multi-line paragraph
join; inline markup combos (bold inside list item); escaping (& < > and
HTML injection attempt `<script>` renders inert); code fence preserves
angle brackets escaped; heading levels 1-6; hr; empty file → empty output;
missing file → clean error. Unique scratch files per test, cleaned up.

## Acceptance ("done means")

1. `meme test` python — 0 failed.
2. Same `--target rust cpp go c ts` — ALL SIX green.
3. `meme build main.meme -o md_bin` — convert a real README-style file,
   show input + output in the transcript.
4. README.md: supported syntax table, CLI usage, escaping rules, tests.
5. Final message: files, test count per target, demo transcript,
   deviations with reasons.
