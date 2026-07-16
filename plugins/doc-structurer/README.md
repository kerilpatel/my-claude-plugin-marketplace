# 🧱 Doc Structurer

> Restructures and polishes a master LaTeX document — structure, phrasing, emphasis — compiles it to PDF, and logs every run.

> ✍️ **Read this first:** this tool only reworks what's *already in* the source document. It restructures, tightens phrasing, and shifts emphasis — it **never invents** a skill, employer, metric, title, or fact. Nothing goes in that wasn't already there.

My main use for it: keeping my resume readable and well-emphasized for whatever context I'm writing toward. Give it that context (pasted, or as a file path) and it summarizes it, extracts the key terms, and proposes a restructuring plan all together in one message — one confirmation from you and it rewords truthful overlaps, compiles a PDF with `tectonic`, and logs the run.

It doubles as a **record-keeping** tool: every run is logged to `APPLICATIONS.md`, and if the folder is a GitHub-linked repo it offers (with your confirmation) to push the output and hands you the GitHub blob link to the PDF — a stable URL for every version you've ever produced.

## ✅ Prerequisites

- [Claude Code](https://claude.com/product/claude-code) with this plugin installed.
- [`tectonic`](https://tectonic-typesetting.github.io/) installed and on your `PATH` — used to compile the reworked document to PDF.
- A master document at `resume.tex`, reachable from your current directory (in it, or somewhere below it). The skill searches for it automatically; if it can't find exactly one match, it asks you for the path.

That's it. Everything else is set up for you on first run: the skill inspects your `resume.tex`, proposes which sections it thinks are safe to edit (summary, skills, experience, projects) versus off-limits (preamble, macros, structural LaTeX), and asks you to confirm or adjust before touching anything. Your answer is saved as `RESUME_STRUCTURE.md` so you're only asked once. `APPLICATIONS.md`, the running log of every run, is created the first time one gets logged.

## ▶️ Usage

```
/doc-structurer:structure-doc [paste the target context, or a path to a file containing it]
```

Run it with no arguments and it will ask you to paste the context you're writing toward.

The flow:

```
┌─────────────────────────────────┐
│        Locate resume.tex        │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│ Review: summary, keywords, plan │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│      You confirm or adjust      │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│    Draft reworked resume.tex    │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│        Pick folder name         │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│           Compile PDF           │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│     Log to APPLICATIONS.md      │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│ Push to GitHub + blob link      │
│ (optional, asks first)          │
└─────────────────────────────────┘
```

## 🗂️ Folder naming

Output folders are named after the **keyword/category** the rework targets (e.g. `python-backend-aws`, `react-frontend`), never a specific company or title. That way, the next time you need a version for a similar category, you can find and reuse the same output instead of starting over.

## ☁️ GitHub record keeping

If your master document lives in a git repo with a GitHub remote, each successful run offers to commit and push just that run's output (the new folder plus the updated `APPLICATIONS.md`) — it always asks before pushing, stages nothing unrelated, and never force-pushes. After the push it gives you the GitHub **blob link** to the PDF, so your application history has a clickable URL per version. No GitHub remote? The step is skipped silently.

## 🛡️ Guardrails

- Never invents a skill, tool, employer, metric, or responsibility that isn't already in the master document — unmatched keywords are reported as "not reflected" instead.
- Never touches the LaTeX preamble, macros, environment definitions, or contact blocks — only the prose sections listed above.
- Never overwrites the master `resume.tex` itself — every reworked version is a copy in its own folder.
