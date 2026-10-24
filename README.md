# diagram-design · DeepSeek Harness skill

**Say "draw me an architecture diagram" — get one self-contained `.html` file with inline SVG, in your brand.**

`MIT` · 45 diagram types · imports `.drawio` / Mermaid / `.excalidraw` · exports SVG + PNG · Python 3.10+ · no build step

<sub>**中文速览**：把一句话需求变成**单文件 HTML + 内联 SVG**的编辑设计风格图表，45 种类型；可从 drawio / Mermaid / Excalidraw 重绘；可按需导出 SVG/PNG。装法见下方 [Install](#install)（一条 `git clone` 到 `~/.dsh/skills/diagram-design`）。</sub>

Editorial diagrams have a look: one accent colour, 1px hairlines, no shadows, every coordinate on a 4px grid. This
skill teaches the agent that style, picks the right layout for the content, and ships the result as a single file you
can double-click open, commit, or paste into a slide.

## What you get

- **One file out.** Inline SVG, Google Fonts optional, no JS beyond an optional motion controller, no external images.
- **45 types, three skins each.** Architecture, delta, flowchart, sequence, state, ER/DB schema, swimlane, quadrant,
  radar, timeline, Gantt, Sankey, Wardley, kanban, journey, UML class, and the rest — minimal light, minimal dark,
  full editorial.
- **Redraw what exists.** `.drawio`, Mermaid blocks and `.excalidraw` files come in as a structure digest and go out
  re-laid-out in the same house style.
- **Brand onboarding.** Point it at your site (or paste tokens) once; later diagrams keep your paper, ink, accent and
  fonts. Profiles live in `~/.diagram-design/profiles/`, outside the install.
- **A real quality gate.** `scripts/self_check.py` rejects broken accessibility, remote assets, off-canvas content and
  malformed motion before you ship.

## Install

The repository root **is** the skill bundle (`SKILL.md` sits at the top), so install = put this directory at
`<skill-root>/diagram-design`.

```bash
# global install: available in every session, every project  (recommended)
git clone https://github.com/jishi7/dsh-diagram-design.git ~/.dsh/skills/diagram-design
```

```powershell
# Windows, same thing
git clone https://github.com/jishi7/dsh-diagram-design.git "$HOME\.dsh\skills\diagram-design"
```

Already have a checkout? Link it instead of cloning twice:

```powershell
New-Item -ItemType Junction -Path "$HOME\.dsh\skills\diagram-design" -Target "<path-to-your-checkout>"
```

```bash
ln -s "<path-to-your-checkout>" ~/.dsh/skills/diagram-design      # macOS / Linux
```

Project-only install: use `<project>\.dsh\skills\diagram-design` as the destination instead (rank 100 beats the global
root). No restart needed — the skill provider watches its roots and republishes the catalog on the next model step.

| Rank | Skill root scanned |
|---|---|
| 100 | `<projectRoot>/.dsh/skills` |
| 200 | `<projectRoot>/.agents/skills` |
| 300 | `customSkillDirs` (config) |
| 400 | `~/.dsh/skills` |
| 500 | `~/.agents/skills` |

## Use

Just describe the diagram:

> Make me an architecture diagram of my app: frontend, backend, database, Redis cache.

> I need a quadrant showing Q2 projects by impact vs effort.

> Redraw this Mermaid block for my deck.

It is also user-invocable as `/diagram-design`. On first use in a project it asks whether to onboard your brand
(palette + fonts) or keep the default skin.

## Requirements

| Feature | Needs |
|---|---|
| HTML/SVG output, self-check | Python 3.10+ (`python` on Windows — not the Microsoft Store `python3` stub) |
| PNG export (`scripts/export_png.py`) | Playwright + Chromium:<br>`python -m pip install playwright && python -m playwright install chromium` |
| Import from draw.io / Mermaid / Excalidraw | nothing beyond Python |

```bash
python scripts/self_check.py <file.html>        # prints OK, or why not
python scripts/export_svg.py <file.html>        # diagram-only SVG next to it
```

## What this repo is not

It is a trimmed, DSH-only remix of [cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design)
(MIT). Removed, because they only ever served other hosts or the maintainer's CI:

- Claude Code / Codex / Factory Droid / Qoder plugin manifests and marketplace docs
- slash commands and Pi prompt templates (here: plain language, or the script directly)
- `docs/` (ADRs, screenshots, hero GIFs), `.github/` workflows, CI lint/verify scripts — issue and PR templates are
  back, as the only thing `.github/` holds
- every path that assumed `skills/diagram-design/` as the install root — this repo's root is the bundle

Kept deliberately: the 200 per-type examples and 5 templates in `assets/`. They are the markup ground truth the model
copies from, not documentation.

## Layout

```
SKILL.md              routing, design system, checklist, DSH environment notes
references/           63 files: 45 type-*.md + style-guide, semantic patterns, export/import, onboarding, profiles
scripts/              6 helpers: self_check, export_svg/png, drawio/mermaid/excalidraw extract
assets/               5 templates, 200 per-type examples, icons.html, index.html gallery
```

`SKILL.md` routes: a request loads one `type-*.md`, plus `primitives-core.md` / `layout-budget.md` only when needed.
Nothing in `references/` is preloaded, so the skill costs a name and a description until it is actually used.

## Update

```bash
git -C ~/.dsh/skills/diagram-design pull     # clone or junction, either way; catalog refreshes itself
```

Upstream can move ahead independently; because this repo relocated the tree, merging `origin/main` needs the moves
re-applied by hand. Re-run the conversion instead of resolving a 600-file rename conflict.

Branches: `dsh` is the default and the only one edited; `main` is a mirror of it, updated on each release so that
`git clone` from either name yields the same tree.

## Licence

MIT — see [LICENSE](LICENSE). Icon licensing (Tabler MIT, Simple Icons CC0, Devicon MIT, log-z MIT) is recorded in
[THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md) and `references/primitive-icons.md`. Original design and text
© Cathryn Lavery; DSH-specific changes (layout, host removal, command references, README) are released under the same
licence.
