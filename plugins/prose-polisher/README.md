# 🪞 Prose Polisher

> Writes a fresh cover letter for each job description — facts from your resume, layout from your master letter — and compiles it straight to PDF.

> ✍️ **Read this first:** every fact in the letter comes from your **resume**. Nothing is invented, and nothing is carried over from the last letter you sent. Your master `cover-letter.tex` supplies the *shape* only — preamble, contact header, salutation, sign-off — never the content.

## 🧱 Two masters, two jobs

This is the part worth understanding, because it's what makes the output different from a find-and-replace on your old letter:

| File | Role | What it provides |
|---|---|---|
| `resume.tex` | **Source of truth** | Every fact, employer, skill, project, and number the letter may use |
| `cover-letter.tex` | **Template only** | Layout, preamble, contact header, salutation, sign-off |

Every run writes a genuinely new body from your resume. The previous letter is never mined for content. Two different job descriptions should produce two visibly different letters, because the evidence is re-selected from scratch against each one.

Why it works this way: a letter built by rewording the last one drifts further from the truth with every application, and drags stale framing and irrelevant metrics along with it. The resume is the reservoir; each JD decides what gets drawn from it.

## ✅ Prerequisites

- [Claude Code](https://claude.com/product/claude-code) with this plugin installed.
- [`tectonic`](https://tectonic-typesetting.github.io/) installed and on your `PATH` — used to compile the letter to PDF.
- A `resume.tex` in the directory you run from (or point at one with `--resume`).

This plugin is standalone — it doesn't read from or depend on any other plugin's files. If `cover-letter.tex` isn't there yet, the first run builds one from your resume's contact details and a couple of questions (how you like to sign off, anything missing from the header). Both masters are read-only forever after; neither is ever overwritten.

## ▶️ Usage

```
/prose-polisher:polish-prose [paste the job description, or a path to a file containing it]
```

Run it with no arguments and it will ask you to paste the job description.

To write against a specific resume version instead of the one at root:

```
/prose-polisher:polish-prose <job description> --resume path/to/other-resume.tex
```

The flow:

```
┌───────────────────────────────────┐
│  Locate resume.tex (facts) +      │
│  cover-letter.tex (layout)        │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│  Gap analysis: rank what the JD   │
│  asks for, match resume evidence  │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│  Write a fresh body around the    │
│  2-3 strongest matches            │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│  Quality gates + mechanical       │
│  dash check                       │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│           Compile PDF             │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│  Push to GitHub + blob link       │
│  (optional, asks first)           │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│      Report result to you         │
└───────────────────────────────────┘
```

## 🗂️ Output

Only the compiled PDF is kept — the `.tex` is a build intermediate and is discarded after compiling. Every run lands flat in `cover-letters/`, named after the organization and designation it was written for:

```
cover-letters/<organization>-<designation>.pdf
```

e.g. `cover-letters/stripe-senior-backend-engineer.pdf`. One PDF per application — nothing gets overwritten by the next run (if a name collides, it asks before replacing). If the job description doesn't state the organization or designation, it asks you before compiling instead of guessing.

## ✂️ How it keeps the letter readable

Each run picks the two or three strongest matches for the role and leaves the rest out, then holds the draft to a set of gates before it compiles:

- **250-400 words, three paragraphs, one page.** Never longer.
- **No em dashes or en dashes.** Not `—`, not `–`, not the LaTeX `---`/`--`. This one is checked mechanically with `grep` against the generated file, not just eyeballed — a hit in the letter body sends it back for a rewrite. (Ordinary hyphens in compound words are fine.)
- **At most two metrics**, and only where the JD actually asks for that dimension of the work. Your resume already carries the numbers; the letter carries the reasoning it can't.
- **No inflated register** (*leverage, spearhead, seamless, passionate*), no stock cadences ("I am excited to apply", "Not only X, but Y"), no rule-of-three padding, no participial windups stacked three deep.
- **No apologetic framing.** Requirements you don't match are reported back to you, not apologized for in the letter.
- **An opening line that couldn't be pasted into a different application.** If it could, it gets rewritten.

If a draft fails a gate, it's revised and re-checked before compiling.

## ☁️ GitHub record keeping

If your masters live in a git repo with a GitHub remote, each successful run offers to commit and push the fresh PDF (it always asks before pushing, stages nothing unrelated, and never force-pushes), then gives you the GitHub **blob link** to it. Since every application has its own PDF, every application gets its own permanent link — a clean clickable record per org and role. No GitHub remote? The step is skipped silently.

## 🛡️ Guardrails

- Never invents a skill, employer, accomplishment, or metric that isn't in `resume.tex` — points with no truthful basis are reported as "not addressed" instead.
- Never carries content forward from the master cover letter or from a previous run.
- Never touches the LaTeX preamble, contact header, date line, salutation, or sign-off block — only the body between salutation and sign-off is authored.
- Never overwrites `resume.tex` or `cover-letter.tex`.
