---
name: npxskillui
description: Install and run skillui, a CLI that reverse-engineers design systems from any website, repo, or local project into a Claude-ready .skill package. Use when the user asks to extract, clone, or replicate a design system, UI style, or visual identity from a URL, repository, or codebase.
---

# skillui — design system extraction

skillui crawls a website, git repo, or local codebase and extracts its complete design system — colors, typography, spacing, animations, components, screenshots — packaged as a folder and `.skill` zip that Claude reads automatically. Pure static analysis, no AI, no API keys.

## Install

Requires Node.js 22.12+.

This fork is not published to npm. Install from GitHub:

```bash
npm install -g https://github.com/nikships/npxskillui/archive/refs/heads/main.tar.gz
```

For ultra mode (scroll frames, hover states, keyframes, video capture), Playwright is also needed:

```bash
npm install -g playwright
npx playwright install chromium
```

If Playwright is missing, skillui still runs in default mode and prints this warning — do not treat it as a failure.

## Usage

```bash
# From a live website (add --mode ultra for full visual extraction)
skillui --url https://example.com

# From a local project directory
skillui --dir ./my-app

# From a git repository (clones to a temp dir, scans, cleans up)
skillui --repo https://github.com/org/repo
```

Useful options:

| Flag | Purpose |
|---|---|
| `--mode ultra` | Cinematic extraction: 7 scroll frames, keyframes, hover/focus diffs, DOM fingerprinting. Needs Playwright. |
| `--out <path>` | Output directory (default: current directory) |
| `--name <str>` | Override the project name used for output folders |
| `--screens <n>` | Ultra mode: max pages to crawl (default 5) |
| `--format <fmt>` | `design-md`, `skill`, or `both` (default) |

## Output

Every run creates a `<name>-design/` folder:

- `DESIGN.md` — colors, fonts, spacing scale, component patterns
- `SKILL.md` + `CLAUDE.md` — instructions Claude Code auto-reads
- `references/` — LAYOUT.md, INTERACTIONS.md, COMPONENTS.md, ANIMATIONS.md (ultra mode)
- `screens/` — page, section, scroll-journey screenshots (ultra mode)
- `tokens/` — colors.json, spacing.json, typography.json
- `<name>.skill` — zip of everything above

## Workflow after extraction

1. Run one of the commands above.
2. Open the output folder in the session working directory: `cd <name>-design`.
3. The design system files are now in context — build UI that matches them, e.g. "Build a landing page that matches this design system".

## Limits

- Default mode extracts CSS tokens only; it does not render the page.
- Ultra mode needs network access and a Chromium download; skip it when the environment is offline.
- Sites that block headless browsers or require login may return partial results. Check the extraction summary (colors/fonts/components counts) to judge completeness.
