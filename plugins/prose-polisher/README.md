# 🪞 Prose Polisher

> Writes a fresh letter for each target context — substance drawn from your master document, layout from your master letter — and compiles it straight to PDF.

> ✍️ **Read this first:** every claim in the letter comes from your **master document**. Nothing is invented, and nothing is carried over from the last letter you produced. Your master letter supplies the *shape* only — preamble, contact header, salutation, sign-off — never the content.

## 🧱 Two masters, two jobs

This is the part worth understanding, because it's what makes the output different from a find-and-replace on the previous letter:

| File | Role | What it provides |
|---|---|---|
| `resume.tex` | **Source of truth** | Every fact, detail, and number the letter may use |
| `cover-letter.tex` | **Template only** | Layout, preamble, contact header, salutation, sign-off |

Every run writes a genuinely new body from the master document. The previous letter is never mined for content. Two different target contexts should produce two visibly different letters, because the supporting material is re-selected from scratch against each one.

Why it works this way: a letter built by rewording the last one drifts further from the truth each time, and drags stale framing and irrelevant numbers along with it. The master document is the reservoir; the target context decides what gets drawn from it.

## ✅ Prerequisites

- [Claude Code](https://claude.com/product/claude-code) with this plugin installed.
- [`tectonic`](https://tectonic-typesetting.github.io/) installed and on your `PATH` — used to compile the letter to PDF.
- A master document at `resume.tex`, reachable from your current directory. The skill searches for it automatically; if it can't find exactly one match, it asks you for the path.

This plugin is standalone — it doesn't read from or depend on any other plugin's files. If `cover-letter.tex` isn't there yet, the first run builds one from your master document's contact details plus a couple of questions (how you like to sign off, anything missing from the header). Both masters are read-only forever after; neither is ever overwritten.

## ▶️ Usage

```
/prose-polisher:polish-prose [paste the target context, or a path to a file containing it]
```

Run it with no arguments and it will ask you to paste the context you're writing toward.

To draw from a specific version of your master document instead of the one it finds:

```
/prose-polisher:polish-prose <target context> --resume path/to/other-document.tex
```

The flow:

```
┌───────────────────────────────────┐
│  Locate master document (facts)   │
│  + master letter (layout)         │
└───────────────────────────────────┘
                 │
                 ▼
┌───────────────────────────────────┐
│  Gap analysis: rank what the      │
│  context asks for, match evidence │
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

One PDF per run — nothing gets overwritten by the next one (if a name collides, it asks before replacing). If the target context doesn't state the organization or designation, it asks you before compiling instead of guessing.

## ✂️ How it keeps the letter readable

Each run picks the two or three strongest matches for the context and leaves the rest out, then holds the draft to a set of gates before it compiles:

- **250-400 words, three paragraphs, one page.** Never longer.
- **No em dashes or en dashes.** Not `—`, not `–`, not the LaTeX `---`/`--`. This one is checked mechanically with `grep` against the generated file, not just eyeballed — a hit in the letter body sends it back for a rewrite. (Ordinary hyphens in compound words are fine.)
- **At most two numbers**, and only where the context actually calls for that dimension of the work. The master document already carries the figures; the letter carries the reasoning it can't.
- **No inflated register** (*leverage, spearhead, seamless, passionate*), no stock cadences ("I am excited to", "Not only X, but Y"), no rule-of-three padding, no participial windups stacked three deep.
- **No apologetic framing.** Points with no truthful basis are reported back to you, not apologized for in the letter.
- **An opening line that couldn't be pasted into a different context.** If it could, it gets rewritten.

If a draft fails a gate, it's revised and re-checked before compiling.

## ☁️ GitHub record keeping

If your masters live in a git repo with a GitHub remote, each successful run offers to commit and push the fresh PDF (it always asks before pushing, stages nothing unrelated, and never force-pushes), then gives you the GitHub **blob link** to it. Since every run has its own PDF, every run gets its own permanent link — a clean clickable record. No GitHub remote? The step is skipped silently.

## 🛡️ Guardrails

- Never invents a skill, employer, accomplishment, or number that isn't already in the master document — points with no truthful basis are reported as "not addressed" instead.
- Never carries content forward from the master letter or from a previous run.
- Never touches the LaTeX preamble, contact header, date line, salutation, or sign-off block — only the body between salutation and sign-off is authored.
- Never overwrites either master file.
