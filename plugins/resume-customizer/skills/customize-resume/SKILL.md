---
description: Tailor a master resume to a job description, compile it with tectonic, and log the application.
argument-hint: [paste the job description, or a path to a file containing it]
allowed-tools: Read, Write, Glob, Bash(mkdir *), Bash(tectonic *), Bash(ls *)
disallowed-tools: Edit
disable-model-invocation: true
---

# Customize Resume for a Job Description

This command works from wherever it's invoked — it doesn't assume a fixed resume location. Step 0 locates the resume folder first; every later step refers back to that resolved path (shown below as `<resume-root>`).

## Step 0 — Locate the master resume

- Search for a `resume.tex` file: check the current working directory first, then search recursively from it (e.g. `Glob **/resume.tex`).
- If exactly one match is found, use its containing directory as `<resume-root>`.
- If none or multiple matches are found, ask the user for the path to their master `resume.tex` and stop until they reply. Derive `<resume-root>` from whatever path they give.
- Remember `<resume-root>` for the rest of this run — never hardcode a path from a previous session.

## Step 1 — Get the job description

- If `$ARGUMENTS` is empty, ask the user to paste the job description now and stop until they reply.
- If `$ARGUMENTS` looks like a file path, read that file as the JD text.
- Otherwise, treat `$ARGUMENTS` verbatim as the JD text.

## Step 2 — Read the master resume and establish its structure

Read `<resume-root>/resume.tex` — the master source. Always use this exact root file, never a previously-tailored subfolder copy, so edits don't compound across applications. Treat it as **read-only for the rest of this run**: it is only ever read, never written or edited — the tailored output always goes to a new file in a new folder (Step 8), never back into this path.

Then resolve which parts of it are safe to edit:

- If `<resume-root>/RESUME_STRUCTURE.md` exists, read it and treat it as binding for this run — it's the user-confirmed record of what's editable.
- If it doesn't exist, this is a first run: inspect `resume.tex` and propose a structure using this default split — editable: SUMMARY prose, SKILLS table rows, EXPERIENCE bullet phrasing, PROJECTS description; off-limits: the LaTeX preamble, package imports, macros (e.g. `\name`, `\address`, `\printaddress`, `\printname`), `rSection`/`rSubsection` environment definitions, spacing-length defs, and header/footer contact blocks. Show the user this proposal mapped to their actual section names, and ask them to confirm or adjust it. Write the confirmed version to `<resume-root>/RESUME_STRUCTURE.md` so future runs use it without asking again.

Finally, read `<resume-root>/APPLICATIONS.md`, if it exists — review past applications so this one doesn't contradict prior phrasing/claims, and to check for an existing folder covering the same keyword category (see Step 7).

## Step 3 — Guardrails (apply throughout Steps 4-6)

- Never fabricate a skill, tool, employer, metric, or responsibility that isn't already present in `resume.tex`.
- Never write to or overwrite `<resume-root>/resume.tex` itself — it is read-only input for every run, tailored or not. The only file ever written is the new copy at `<resume-root>/<folder>/resume.tex` (Step 6).
- Only edit the sections marked editable in the Step 2 structure; never touch anything marked off-limits there.
- Preserve exact section order, LaTeX environment structure, spacing commands (e.g. `\setlength{\itemsep}`), tabular row syntax, dates, titles, and company names.
- Escape LaTeX special characters (`% & $ # _ { } ~ ^ \`) in any new or edited prose.
- If a JD keyword has no truthful basis anywhere in the resume, do not insert it — instead note it in the final report as "not reflected — no matching experience."

## Step 4 — Build and present the full review in one shot

Do all of the following analysis up front, and present it to the user together, in a single message:

1. **JD summary** — role title, company (state explicitly if no company name is given), seniority, key responsibilities, and required qualifications.
2. **Keywords** — extracted from the JD, grouped into: Languages/Tools, Frameworks, Concepts/Practices, Domain/Soft skills.
3. **Mismatch/gap flags** — anything that looks like a bad fit or a minimum-qualification gap (e.g. required years of experience, a required clearance/degree/certification, a core tech stack the resume doesn't show). Say plainly if nothing stands out.
4. **Tailoring plan** — section by section (Summary / Skills / Experience / Projects), what you intend to reword or re-emphasize and which keyword(s) each change targets. Keep it short — a few bullets per section, not full rewritten prose. Ground this in `resume.tex` (Step 2) and cross-check `APPLICATIONS.md` (if present) so it doesn't contradict prior phrasing/claims.

## Step 5 — Single confirmation gate

Ask the user once, covering everything from Step 4 together: do they want to proceed with this plan, adjust something (add a truthful detail you missed, drop a proposed change, redirect emphasis, override a flagged gap), or abort?

- If they confirm as-is, proceed straight to Step 6.
- If they give adjustments, incorporate that feedback into the plan and proceed straight to Step 6 — do not re-present the plan for a second round of approval unless they explicitly ask to see it again first.
- If they say to abort, stop here entirely — do not draft anything, create a folder, or log an entry.

## Step 6 — Draft the tailored resume.tex

Apply the confirmed plan per the Step 3 guardrails: rephrase existing SUMMARY/SKILLS/EXPERIENCE/PROJECTS content toward the JD's terminology where it's truthfully applicable, without adding claims that aren't already backed by the master resume.

This is a draft held in working memory only — do not write or touch any file yet, and never the master `<resume-root>/resume.tex`. The first and only time this draft is written to disk is Step 8, and it's written to the new folder, not the master file's path.

## Step 7 — Pick a folder name

Name the folder after the **keyword/category** this tailoring targets, never after the company or role title — the goal is that a future application to a *different* company for a similar-category role can find and reuse this same tailored resume.

- Derive the name from the 2-3 most dominant technical keywords/category from Step 4 (e.g. `python-backend-aws`, `react-frontend`, `data-pipeline-etl`). Kebab-case, at most 4 words.
- Before creating a new folder, check `<resume-root>` for an existing folder whose name matches or closely overlaps this category. If one exists, tell the user and ask whether to reuse/update that folder or create a new variant (append `-2`, `-3`, etc.) rather than silently overwriting or duplicating.
- If no category can be confidently derived, fall back to a generic label plus a date disambiguator (e.g. `general-2026-07`).

## Step 8 — Write & compile

1. `mkdir -p <resume-root>/<folder>`
2. Write the tailored LaTeX to `<resume-root>/<folder>/resume.tex` — a brand-new file in the new folder. Do not write to, overwrite, or copy over `<resume-root>/resume.tex`; that file must be byte-for-byte unchanged at the end of this run.
3. Run `tectonic resume.tex` from inside that folder (e.g. `cd <resume-root>/<folder> && tectonic resume.tex`)
4. Capture whether it succeeded, and the full tectonic output either way.

## Step 9 — Log to APPLICATIONS.md

Read `<resume-root>/APPLICATIONS.md`. If it doesn't exist, create it with this header first:

```markdown
# Application History

Tracks every tailored resume generated by /customize-resume.
Read this before tailoring a new resume to avoid contradicting past phrasing/claims,
and to spot an existing folder for the same keyword category.

| Date | Folder | Category | Company | Role | Keywords Added/Emphasized | Status |
|------|--------|----------|---------|------|----------------------------|--------|
```

Append one row for this run — even if the compile failed (use `Failed: <short reason>` as the Status in that case, otherwise `Compiled`). Use `Unknown` for Company/Role if the JD didn't specify them — never leave a cell blank. "Category" is the keyword/category the folder is named after (from Step 7). "Keywords Added/Emphasized" should list only what was actually woven into this resume (semicolon-separated), not the full keyword list from Step 4.

## Step 10 — Report to the user

Present: the JD summary, the categorized keyword list, which keywords were woven in vs. not reflected, the new folder path, the compile result (success + PDF path, or the raw tectonic error), and the row added to `APPLICATIONS.md`.
