# 📄 Resume Customizer

> Tailor your master LaTeX resume to a job description, compile it, and keep a log of every application.

Give it a job description (pasted or as a file path) and it will: summarize the JD, extract keywords, check with you before doing any work, propose a short tailoring plan for your review, weave in truthful keyword overlaps, compile a PDF with `tectonic`, and log the run.

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
1. Locates your `resume.tex`.
2. Summarizes the JD and extracts keywords (Languages/Tools, Frameworks, Concepts/Practices, Domain/Soft skills).
3. **Checks in with you** before doing any work — flags mismatches or qualification gaps, and stops if you say to abort.
4. Reads your master resume, confirms which sections are safe to edit (only asked once — saved to `RESUME_STRUCTURE.md`), and checks past applications for context.
5. **Proposes a short tailoring plan** section by section so you can validate, adjust, or add details before anything is written.
6. Drafts the tailored `resume.tex`, staying truthful to what's already in your master resume.
7. Picks a folder name and compiles the PDF.
8. Logs the application to `APPLICATIONS.md`.

## 🗂️ Folder naming

Output folders are named after the **keyword/category** the tailoring targets (e.g. `python-backend-aws`, `react-frontend`), never the company or role. That way, the next time you apply to a *different* company for a similar-category role, you can find and reuse the same tailored resume instead of starting over.

## 🛡️ Guardrails

- Never invents a skill, tool, employer, metric, or responsibility that isn't already in your master resume — unmatched JD keywords are reported as "not reflected" instead.
- Never touches the LaTeX preamble, macros, environment definitions, or contact blocks — only the prose sections listed above.
