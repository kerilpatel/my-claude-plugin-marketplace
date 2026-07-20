# 🧩 My Claude Plugin Marketplace

> My Claude plugins that make my life easier — or, less politely, so I don't bang my head 🧱 on the wall while working with LLMs.

This is my personal stash 🗃️ of [Claude Code](https://claude.com/product/claude-code) plugins — the little bits of automation I got tired of doing by hand, so now Claude does them instead. Repetitive tasks 🔁, muscle-memory workflows, the "ugh, not this again" 😩 moments — if it happens more than twice, it probably ends up here.

No grand roadmap 🗺️, no promises of stability. Just tools that make my life easier ✨, built as I need them.

## 📦 Install

```
/plugin marketplace add kerilpatel/my-claude-plugin-marketplace
```

Then browse and install with:

```
/plugin
```

## 🔌 Plugins

> ✍️ **A note on the writing tools below:** they only rework what's *already true* in a document — restructure, fix phrasing, shift emphasis. They never invent a skill, employer, number, or fact. Polish, not fiction.

| Plugin | What it does |
|---|---|
| [`doc-structurer`](plugins/doc-structurer) | Restructures and polishes a master LaTeX document for a target context via `/doc-structurer:structure-doc` — proposes a rework plan, rewords truthful overlaps, compiles a PDF, logs the run, and (with confirmation) pushes to GitHub with a blob link back, for record keeping. |
| [`prose-polisher`](plugins/prose-polisher) | Writes a fresh letter for a target context via `/prose-polisher:polish-prose` — substance drawn from a master LaTeX document, layout from a master letter — compiles straight to PDF, and (with confirmation) pushes to GitHub with a blob link back, for record keeping. |

## 🤔 Why this exists

Because copy-pasting the same prompt for the fifth time in a week is how you know it's time to automate it. 🤖⚡
