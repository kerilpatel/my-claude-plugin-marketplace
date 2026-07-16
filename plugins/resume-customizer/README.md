# 📄 Resume Customizer

> Tailor your master LaTeX resume to a job description, compile it, and keep a log of every application.

Give it a job description (pasted or as a file path) and it will summarize the JD, extract keywords, and propose a tailoring plan all together in one message — one confirmation from you and it weaves in truthful keyword overlaps, compiles a PDF with `tectonic`, and logs the run.

> 🎯 **Quick word on purpose:** this thing isn't here to help you fib your way into a job. Its whole reason for existing is to re-emphasize and reword what's *already true* about you, so the resume speaks the JD's language — no invented skills, no made-up years of experience, no fictional job titles. A resume like that wastes everyone's time: yours when you get found out, and the recruiter's for reading it. Good tailoring is just honest framing, done well.

## ✅ Prerequisites

- [Claude Code](https://claude.com/product/claude-code) with this plugin installed.
- [`tectonic`](https://tectonic-typesetting.github.io/) installed and on your `PATH` — used to compile the tailored resume to PDF.
- A master resume at `resume.tex`, reachable from your current directory (in it, or somewhere below it). The skill searches for it automatically; if it can't find exactly one match, it asks you for the path.

That's it. Everything else is set up for you on first run: the skill inspects your `resume.tex`, proposes which sections it thinks are safe to edit (summary, skills, experience, projects) versus off-limits (preamble, macros, structural LaTeX), and asks you to confirm or adjust before touching anything. Your answer is saved as `RESUME_STRUCTURE.md` so you're only asked once. `APPLICATIONS.md`, the running log of every tailored application, is created the first time a resume gets logged.

## ▶️ Usage

```
/resume-customizer:customize-resume [paste the job description, or a path to a file containing it]
```

Run it with no arguments and it will ask you to paste the JD.

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
│    Draft tailored resume.tex    │
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
```

## 🗂️ Folder naming

Output folders are named after the **keyword/category** the tailoring targets (e.g. `python-backend-aws`, `react-frontend`), never the company or role. That way, the next time you apply to a *different* company for a similar-category role, you can find and reuse the same tailored resume instead of starting over.

## 🛡️ Guardrails

- Never invents a skill, tool, employer, metric, or responsibility that isn't already in your master resume — unmatched JD keywords are reported as "not reflected" instead.
- Never touches the LaTeX preamble, macros, environment definitions, or contact blocks — only the prose sections listed above.
