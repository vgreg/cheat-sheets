# Neovim (LazyVim) — Motions, Actions & Macros

## Legend

- Combine: **operator** + **motion** + _(optional)_ **count** → action
- `{}` = text object; `()` = motion; `[]` = examples

## Core Motions

- **Basic:** `h j k l`, `0` start, `^` first nonblank, `$` end
- **Words:** `w e b ge` (BIG: `W E B`)
- **Sent/Para:** `) (` and `{ }`
- **Find on line:** `f{c}` to `{c}`, `t{c}` before `{c}`, `;`/`,` repeat/rev, `F`/`T` back
- **Pairs:** `%` between `() [] {}`
- **Search:** `/pat`, `?pat`, `n/N`, `*`/`#` on word under cursor
- **File pos:** `gg`, `G`, `{count}G`, `H/M/L`
- **Jumps/Marks:** `Ctrl-o/i` older/newer, `m{a}`, `` `{a}`exact,`'{a}` line
- **Functions:** `]m` / `[m` → jump to next / previous **function** start (`]M` / `[M` for end)
- **Classes:** `]c` / `[c` → jump to next / previous **class** start (`]C` / `[C` for end)

## Text Objects `{…}` (LazyVim + mini.ai)

- **Words:** `iw` / `aw` (big: `iW` / `aW`)
- **Quotes (all kinds):** `iq` / `aq` (covers `" ' ``)
- **Brackets ((), {}, [], <>):** `ib` / `ab` (balanced blocks)
- **Explicit pairs:** `i(`/`a(`, `i{`/`a{`, `i[`/`a[`, `i<`/`a<`
- **Paragraph/Sentence:** `ip`/`ap`, `is`/`as`, **Tags:** `it`/`at`
  _mini.ai provides `q` (quotes) and `b` (brackets) aliases by default._ ([GitHub][1])

## Operators (Actions)

- **Delete:** `d` → `dw`, `d$`, `diw`
- **Change:** `c` (delete+Insert) → `ciw`, `c$`, `ct,`
- **Yank:** `y` → `yy`, `yaw`, `y3j`; **Paste:** `p` after, `P` before; from reg `"ap`
- **Case:** `gu` lower, `gU` upper, `g~` toggle (e.g., `gUw`, `gUiw`)
- **Indent/Format:** `>` indent, `<` dedent, `=` reformat (e.g., `=i{`, `=%`)
- **Replace char:** `r{c}`; **substitute char/line:** `s` / `S`
- **Join:** `J` (space), `gJ` (no space)

**Pattern:** `{count}{operator}{motion}` → `3dw`, `d}`, `ci"`, `=i{`

## Insert/Edit

- **Insert:** `i` before, `a` after, `I` BOL nonblank, `A` EOL
- **New lines:** `o` below, `O` above
- **Change to EOL/BOL:** `C` (= `c$`), `c0`, `c^`
- **Replace mode:** `R`
- **Inc/Dec number:** `Ctrl-a` / `Ctrl-x` (prefix count for step)

## Visual

- `v` char, `V` line, `Ctrl-v` block → then `d c y > < = …`
- `.` repeats the last change (works after Visual indents)

## Registers 🗂

- Default `""`, **named** `"a`..`"z`, **yank-only** `"0`, **numbered** `"1`..`"9`
- System: `"*` (selection), `"+` (clipboard); black hole: `"_`; expr: `"=`
- List: `:registers`

## Macros 🎛️

- **Record:** `q{a}` start → do stuff → `q` stop
- **Play:** `@a`, repeat last: `@@`, with count: `10@a`
- **Append:** `qA` (capital letter)
- **Edit:** `:let @a='…'` (or `:put a` into a scratch buffer, edit, yank back)
- **Run over range:** Visual lines → `:normal @a`; whole file `:%normal @a`; matches `:g/{pat}/normal @a`
- **Tip:** Prefer motions/text objects over raw `hjkl` for robust macros

## Search & Substitute

- Word under cursor: `*`/`#`; change next match: `cgn` (then `.`)
- Substitute: `:s/foo/bar/gc` (line), `:%s/\vfoo+/bar/g` (file), visual `:'<,'>s/.../`

## Buffers (LazyVim defaults)

- **Next/Prev:** `<S-l>` / `<S-h>` or `]b` / `[b`
- **Picker (buffers):** `<leader>,` or `<leader>fb`
- **Switch to other buffer (toggle):** `<leader>`` or `<leader>bb`
- **Close:** `<leader>bd` (delete), `<leader>bo` (others), `<leader>bD` (buffer+window)
- **Pin & clean:** `<leader>bp` pin, `<leader>bP` delete non-pinned, `<leader>bl`/`<leader>br` delete left/right buffers
  _Keymap refs._ ([LazyVim][2])

## Windows & Tabs (LazyVim)

- **Move focus:** `Ctrl-h/j/k/l`
- **Resize:** `Ctrl-↑/↓/←/→`
- **Split:** `<leader>-` horizontal, `<leader>|` vertical
- **Close/Zoom:** `<leader>wd` close window, `<leader>wm` or `<leader>uZ` toggle zoom; Zen: `<leader>uz`
- **Window Hydra (helper menu):** `Ctrl-w` then `<space>`
- **Tabs:** new `<leader><tab><tab>`, next/prev `<leader><tab>]` / `<leader><tab>[`, close `<leader><tab>d`, first/last `<leader><tab>f` / `<leader><tab>l`
  _Keymap refs._ ([LazyVim][2])

## Files & Pickers (LazyVim Snacks/Telescope)

- **Find files (root/cwd):** `<leader><space>` / `<leader>fF`
- **Grep (root):** `<leader>/`; recent: `<leader>fr`; marks: `<leader>sm`; registers: `<leader>s"`; keymaps: `<leader>sk`
- **File explorer:** `<leader>e` (root), `<leader>E` (cwd)
  _Keymap refs._ ([LazyVim][2])

## LSP (quick hits)

- **Go to:** `gd` def, `gr` refs, `gI` impl, `gy` type, `gD` decl
- **Docs:** `K`; **rename:** `<leader>cr`; **code action:** `<leader>ca`
- **Diagnostics:** `]d/[d` next/prev, line diag `<leader>cd`, format `<leader>cf`
  _Keymap refs._ ([LazyVim][2])

## Formatting & Flow

- **Autoformat toggle:** `<leader>uf` (global), `<leader>uF` (buffer)
- **Reflow paragraph:** `gqap` (or Visual → `gq`)
- **Show invisibles:** `:set list listchars=tab:▸\\ ,trail:·`

## Jumps & History

- **Jump list:** `:jumps`; back/forward `Ctrl-o`/`Ctrl-i`
- **Change list:** `:changes`; last change: `` `. ``
