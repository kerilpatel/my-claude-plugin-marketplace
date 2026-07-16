# 🪞 Prose Polisher

> Polishes the prose of a master LaTeX letter — grammar, flow, emphasis — and compiles it straight to PDF.

> ✍️ **Read this first:** this tool only reworks what's *already in* the source letter. It fixes grammar, smooths flow, and shifts emphasis — it **never invents** an employer, skill, accomplishment, or number. Nothing goes in that wasn't already there.

Give it the context you're writing toward (pasted, or as a file path) and it reworks the opening line, body, and closing so the letter reads clearly for that context, then compiles straight to a PDF — no confirmation step, no log to maintain.

It also helps with **record keeping**: if the folder is a GitHub-linked repo, it offers (with your confirmation) to push the fresh PDF and hands you the GitHub blob link — one clickable URL for your records.

## ✅ Prerequisites

- [Claude Code](https://claude.com/product/claude-code) with this plugin installed.
- [`tectonic`](https://tectonic-typesetting.github.io/) installed and on your `PATH` — used to compile the polished letter to PDF.

Nothing else is required up front. This plugin is standalone — it doesn't read from or depend on any other plugin's files. The master letter always lives at `cover-letter.tex` in the directory you run the command from. If it's not there yet, the first run asks a few questions (contact info, a short narrative about you, your key strengths, how you like to sign off) and generates it for you as your durable master. Every later run polishes a copy of that master; the master itself is never overwritten.

## ▶️ Usage

```
/prose-polisher:polish-prose [paste the target context, or a path to a file containing it]
```

Run it with no arguments and it will ask you to paste the context you're writing toward.

The flow:

```
┌───────────────────────────────────┐
│ Locate or create cover-letter.tex  │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│  Rework opening line / body /      │
│  closing for the target context    │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│           Compile PDF              │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│  Push to GitHub + blob link        │
│  (optional, asks first)            │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│      Report result to you          │
└───────────────────────────────────┘
```

## 🗂️ Output

Only the compiled PDF is kept — the polished `.tex` is a build intermediate and is discarded after compiling. Every run lands flat in `cover-letters/`, named after the organization and designation it was written for:

```
cover-letters/<organization>-<designation>.pdf
```

e.g. `cover-letters/stripe-senior-backend-engineer.pdf`. One PDF per application — nothing gets overwritten by the next run (if a name collides, it asks before replacing). If the target context doesn't state the organization or designation, it asks you before compiling instead of guessing.

## ☁️ GitHub record keeping

If your master letter lives in a git repo with a GitHub remote, each successful run offers to commit and push the fresh PDF (it always asks before pushing, stages nothing unrelated, and never force-pushes), then gives you the GitHub **blob link** to it. Since every application has its own PDF, every application gets its own permanent link — a clean clickable record per org and role. No GitHub remote? The step is skipped silently.

## 🛡️ Guardrails

- Never invents a skill, employer, accomplishment, or metric that isn't already in the master letter — points with no truthful basis are reported as "not addressed" instead.
- Never touches the LaTeX preamble, contact header, date line, salutation, or sign-off block — only the opening line, body paragraph(s), and closing line.
- Never overwrites `cover-letter.tex` itself.
