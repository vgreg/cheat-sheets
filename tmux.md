# Tmux — Quick Cheat Sheet (Prefix = `C-a`)

_Compact reference for sessions, windows, panes, copy mode, and persistence. Assumes prefix remapped to `C-a`._

## Legend

- `C-a` = prefix
- `:` = command prompt
- `{n}` = optional number/index

## Sessions 🌀

- **New:** `tmux new -s name`
- **List:** `tmux ls` or `C-a s` (interactive)
- **Attach:** `tmux a -t name`
- **Detach:** `C-a d`
- **Rename:** `C-a $`
- **Switch:** `C-a (` prev / `)` next / `s` choose / `L` last
- **Kill:** `tmux kill-session -t name`
- **Start detached:** `tmux new -d -s name`

## Windows 🪟

- **New:** `C-a c`
- **Next / Prev:** `C-a n` / `C-a p`
- **Select by number:** `C-a {n}`
- **Rename:** `C-a ,`
- **Close:** `C-a &`
- **List:** `C-a w`
- **Move / Swap:** `:move-window -t {n}` / `:swap-window -t {n}`

## Panes 🪜

- **Split V:** `C-a %`
- **Split H:** `C-a "`
- **Switch:** `C-a` arrows or `o`
- **Resize:** `C-a Alt` + arrows
- **Swap:** `C-a {` up / `}` down
- **Zoom toggle:** `C-a z`
- **Kill:** `C-a x`
- **Break to new window:** `C-a !`
- **Join from another window:** `:join-pane -s :{src}`

## Copy Mode 📋

- **Enter:** `C-a [`
- **Exit:** `q` or `Esc`
- **Scroll:** arrows or `PgUp/PgDn`
- **Search:** `/` forward, `?` backward, `n/N` repeat
- **Start selection:** `Space`
- **Copy:** `Enter`
- **Paste:** `C-a ]`
- **Copy to system clipboard:** `y` (if bound via `copy-pipe-and-cancel`)

## Persistence 💾 (tmux-resurrect + tmux-continuum)

- **Manual save:** `C-a Ctrl-s`
- **Manual restore:** `C-a Ctrl-r`
- **Auto-save & restore:** handled by continuum (once enabled in `.tmux.conf`)

  ```tmux
  set -g @plugin 'tmux-plugins/tmux-resurrect'
  set -g @plugin 'tmux-plugins/tmux-continuum'
  set -g @continuum-restore 'on'
  run '~/.tmux/plugins/tpm/tpm'
  ```

- Saved state includes: sessions, windows, panes, layouts, cwd, and optionally environment.

## Project Layouts 🏗️ (tmuxinator)

- Define reusable layouts in `~/.config/tmuxinator/`:

  ```yaml
  name: work
  root: ~/project
  windows:
    - editor:
        layout: main-vertical
        panes:
          - nvim
          - git status
    - server:
        panes:
          - cd backend && npm run dev
  ```

- **Start layout:** `tmuxinator start work` (or `mux start work`)
- **List:** `tmuxinator list`
- **Edit:** `tmuxinator edit work`
- **Delete:** `tmuxinator delete work`
- Ideal for structured project workspaces.

## Misc Commands ⚙️

- **Command prompt:** `C-a :`
- **Reload config:** `C-a :source-file ~/.tmux.conf`
- **List key bindings:** `tmux list-keys` or `C-a ?`
- **Show messages:** `C-a ~`
- **Clock mode:** `C-a t`
- **Lock session:** `C-a C-l`
- **Pass prefix:** `C-a C-a` (literal `C-a` to nested tmux)

## Useful CLI 🧰

- `tmux attach -t name`
- `tmux new -s name`
- `tmux kill-session -t name` / `tmux kill-server`
- `tmux list-keys`
- `tmux list-windows -t name`
- `tmux list-panes -a`

## Power Tips 💡

- Use **tmux-resurrect** for quick save/restore between boots.
- Use **tmuxinator** for reproducible dev environments.
- Use **`C-a C-a`** to avoid conflicts when nesting tmux in SSH sessions.
- **`C-a L`** jumps to last session fast.
- Combine panes and tmuxinator to make project startup trivial.

## Minimal Survival Kit

- Sessions: `tmux new -s foo` / `tmux a -t foo` / `C-a d`
- Windows: `C-a c` `n` `p` `,` `&`
- Panes: `C-a %` `"` arrows `z` `x`
- Copy: `C-a [` `Space` `Enter` `C-a ]`
- Save/Restore: `C-a Ctrl-s` / `C-a Ctrl-r`
- tmuxinator: `mux start project`
