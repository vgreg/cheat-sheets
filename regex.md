# Regular Expressions — Quick Cheat Sheet (Python focus, Neovim notes)

_Short, practical, stackable. Prefer Python raw strings: `r"...""`._

## Core Atoms

- **Literals:** `abc`, escaped: `\.` `\*` `\?`
- **Any char:** `.` (not newline unless DOTALL)
- **Char classes:** `[abc]`, ranges `[a-z]`, negation `[^…]`
- **Predefined:** `\d` digit, `\w` word, `\s` whitespace
  `\D` non-digit, `\W` non-word, `\S` non-space

**Neovim**: `\w`/`\W` depend on `'iskeyword'`. Newline is **never** matched by `.`; use `\_.` to include newline.

## Quantifiers

- `a*` 0+, `a+` 1+, `a?` 0/1, `a{m}`, `a{m,n}`
- **Non-greedy:** `*?` `+?` `??` `{m,n}?`

## Anchors & Boundaries

- `^` start, `$` end (Python: per line with `re.M`; Neovim: per line by default)
- `\b` word boundary, `\B` non-boundary

## Groups & Backrefs

- **Capture:** `( … )`, backref: `\1`, `\2`
- **Non-capturing:** `(?: … )`
- **Named (Python):** `(?P<name>…)`, backref: `(?P=name)` or `\g<name>`
- **Alternation:** `A|B`

**Neovim**: backref in `:s` replacement is `\1`, `\0` whole match. No Python-style `(?P<name>)`; uses `\%(...\)` for some special groups (see Lookaround below).

## Lookarounds

- **Python:**
  - Lookahead: `X(?=Y)` / negative `X(?!Y)`
  - Lookbehind: `(?<=X)Y` / negative `(?<!X)Y`

- **Neovim (Vim-regex syntax):**
  - Lookahead: `X\@=`, negative: `X\@!`
  - Lookbehind: `\@<=Y` (positive before Y), negative: `\@<!Y`
  - Start/end of match anchors: `\zs` (set start), `\ze` (set end)

## Flags / Modes

- **Python `re` flags:**
  - `re.I` (ignorecase), `re.M` (multiline), `re.S` (DOTALL), `re.X` (verbose), `re.A` (ASCII), `re.U` (Unicode default ≥3.3)
  - Inline: `(?i)`, `(?m)`, `(?s)`, `(?x)` etc.

- **Neovim search/sub flags (after pattern):**
  - `\c` ignorecase / `\C` case-sensitive
  - In `:s///`: `g` (global on line), `c` (confirm), `i`/`I` (ignore/force case), `n` (count only)

## Matching APIs

- **Python:**

  ```py
  import re
  m = re.search(r'\bfoo\b', s)  # first
  ms = re.findall(r'foo(\d+)', s)  # list of captures
  it = re.finditer(r'…', s)  # iterator of Match
  out = re.sub(r'cat', 'dog', s, count=1, flags=re.I)
  out = re.sub(r'(\d{4})-(\d{2})-(\d{2})', r'\3/\2/\1', s)
  ```

- **Neovim:**
  - Search forward: `/pattern` then `n/N`
  - Replace: `:%s/pat/repl/g` (whole file), `:'<,'>s/pat/repl/g` (visual)

## Replacement Syntax

- **Python:** replacement uses `\1` or `\g<name>`, or a **function**:

  ```py
  re.sub(r'\d+', lambda m: str(int(m.group())+1), s)
  ```

- **Neovim `:s`:** `\0` whole match, `\1`.. groups, `&` last replacement, `~` last used replacement text.
  - Magic chars in **replacement**: `\r` newline, `\t` tab, `\\` backslash, `\n` literal “n”.

## “Magic” in Neovim (escaping rules)

- Default is “magic-ish”. To simplify, use **Very Magic**: add `\v` at pattern start → most chars are literals, metachars are `.^$[](){}*+?|` only.
  - Example: default: `/\(\d\+\)\s\+\(\w\+\)/`
    with `\v`: `/\v(\d+)\s+(\w+)/`

## Multiline & Newlines

- **Python:** `.` matches newline with `re.S`; `^/$` match line starts/ends with `re.M`.
- **Neovim:** `^/$` always line-based, `.` **never** matches newline; use `\_s` (any whitespace incl. newline) or `\_.` (any char incl. newline).
  - Example (paragraph): `/\vfoo\_.*bar`

## Common Recipes

- **Trim spaces:** Python: `re.sub(r'[ \t]+$', '', s, flags=re.M)`; Neovim: `:%s/\s\+$//`
- **CSV field (naïve):** `\v([^,]+)|"([^"]*)"`, Python use `csv` module instead if serious.
- **ISO date:** `\b\d{4}-\d{2}-\d{2}\b`
- **Hex color:** `\v#([0-9a-fA-F]{3}|[0-9a-fA-F]{6})\b`
- **Balanced quotes (content):** Python: `r'"([^"\\]|\\.)*"'` (handles escapes)
- **Replace only whole words “cat”→“dog”:** Python: `re.sub(r'\bcat\b', 'dog', s)`, Neovim: `:%s/\<cat\>/dog/g`

## Performance & Gotchas

- Prefer explicit classes over `.` when possible; use non-greedy + anchors.
- Avoid catastrophic backtracking: favor atomic-ish patterns and unambiguous quantifiers (e.g., `[^"]*` over `.*?` when safe).
- **Python Unicode:** `\w` includes digits/letters/underscore (Unicode by default). Use `re.A` to force ASCII.
- **Neovim word boundaries:** `\<` and `\>` depend on `'iskeyword'`.

## Quick Diff Table (Python vs Neovim)

| Feature             | Python              | Neovim                                                                    |
| ------------------- | ------------------- | ------------------------------------------------------------------------- |
| Dot matches newline | `re.S` (DOTALL)     | Use `\_.`                                                                 |
| Multiline `^/$`     | `re.M`              | Default (line-based)                                                      |
| Ignorecase          | `re.I` or `(?i)`    | `\c` (inline) or `:s///i`                                                 |
| Lookahead           | `(?=…)` / `(?!…)`   | `…\@=` / `…\@!`                                                           |
| Lookbehind          | `(?<=…)` / `(?<!…)` | `\@<=…` / `\@<!…`                                                         |
| Named groups        | `(?P<name>…)`       | ❌ (use numbered)                                                         |
| Non-capturing       | `(?:…)`             | `\%(…\)` (special group), or just capture and ignore                      |
| Word boundary       | `\b`                | `\<` (start), `\>` (end), `\b` also works but influenced by `'iskeyword'` |
| Very magic          | n/a                 | `\v` at start                                                             |
| Replace refs        | `\1` / `\g<name>`   | `\0`, `\1`, …; `&` last repl                                              |

## Minimal Survival Kit 💡

- **Python:** `re.findall(r'\b\w+\b', s)`, `re.sub(r'\s+', ' ', s)`, flags via `re.X|re.M|re.S`
- **Neovim:** `/\v(\w+)\s+\1` find dup words; `:%s/\v(\S)\s+([,.;])/\1\2/g` tighten punctuation; `:%s/\s\+$//`

_If it feels too hard, try `\v` (Neovim) or `re.X` (Python verbose) and build patterns incrementally._
