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

## Step 2 — Summarize the JD

Summarize back to the user: role title, company (state explicitly if no company name is given), seniority, key responsibilities, and required qualifications.

## Step 3 — Extract & show keywords

Extract keywords from the JD and show them to the user grouped into: Languages/Tools, Frameworks, Concepts/Practices, Domain/Soft skills.

## Step 4 — Confirm before proceeding

Before doing any tailoring work, ask the user to confirm they want to proceed with this JD. Use the Step 2 summary and Step 3 keywords as the basis, and explicitly flag anything that looks like a mismatch or a minimum-qualification gap (e.g. required years of experience, a required clearance/degree/certification, a core tech stack the resume doesn't show) so a bad-fit JD doesn't get tailored by default.

- If the user confirms (or overrides a flagged concern and says to continue anyway), proceed to Step 5.
- If the user says to abort, stop here entirely — do not read source material, draft anything, create a folder, or log an entry.

## Step 5 — Read the master resume and establish its structure

Read `<resume-root>/resume.tex` — the master source. Always use this exact root file, never a previously-tailored subfolder copy, so edits don't compound across applications. Treat it as **read-only for the rest of this run**: it is only ever read, never written or edited — the tailored output always goes to a new file in a new folder (Step 10), never back into this path.

Then resolve which parts of it are safe to edit:

- If `<resume-root>/RESUME_STRUCTURE.md` exists, read it and treat it as binding for this run — it's the user-confirmed record of what's editable.
- If it doesn't exist, this is a first run: inspect `resume.tex` and propose a structure using this default split — editable: SUMMARY prose, SKILLS table rows, EXPERIENCE bullet phrasing, PROJECTS description; off-limits: the LaTeX preamble, package imports, macros (e.g. `\name`, `\address`, `\printaddress`, `\printname`), `rSection`/`rSubsection` environment definitions, spacing-length defs, and header/footer contact blocks. Show the user this proposal mapped to their actual section names, and ask them to confirm or adjust it. Write the confirmed version to `<resume-root>/RESUME_STRUCTURE.md` so future runs use it without asking again.

Finally, read `<resume-root>/APPLICATIONS.md`, if it exists — review past applications so this one doesn't contradict prior phrasing/claims, and to check for an existing folder covering the same keyword category (see Step 9).

## Step 6 — Guardrails (apply throughout Steps 7-8)

- Never fabricate a skill, tool, employer, metric, or responsibility that isn't already present in `resume.tex`.
- Never write to or overwrite `<resume-root>/resume.tex` itself — it is read-only input for every run, tailored or not. The only file ever written is the new copy at `<resume-root>/<folder>/resume.tex` (Step 10).
- Only edit the sections marked editable in the Step 5 structure; never touch anything marked off-limits there.
- Preserve exact section order, LaTeX environment structure, spacing commands (e.g. `\setlength{\itemsep}`), tabular row syntax, dates, titles, and company names.
- Escape LaTeX special characters (`% & $ # _ { } ~ ^ \`) in any new or edited prose.
- If a JD keyword has no truthful basis anywhere in the resume, do not insert it — instead note it in the final report as "not reflected — no matching experience."

## Step 7 — Propose the tailoring plan

Before editing anything, briefly explain the planned changes to the user, section by section (Summary / Skills / Experience / Projects): what you intend to reword or re-emphasize and which keyword(s) each change targets. Keep it short — a few bullets per section, not full rewritten prose.

Ask the user to validate the plan or adjust it — they may want to add a truthful detail you didn't pick up on, drop a proposed change, or redirect emphasis. Incorporate their feedback before moving on.

## Step 8 — Draft the tailored resume.tex

Apply the validated plan per the Step 6 guardrails: rephrase existing SUMMARY/SKILLS/EXPERIENCE/PROJECTS content toward the JD's terminology where it's truthfully applicable, without adding claims that aren't already backed by the master resume.

This is a draft held in working memory only — do not write or touch any file yet, and never the master `<resume-root>/resume.tex`. The first and only time this draft is written to disk is Step 10, and it's written to the new folder, not the master file's path.

## Step 9 — Pick a folder name

Name the folder after the **keyword/category** this tailoring targets, never after the company or role title — the goal is that a future application to a *different* company for a similar-category role can find and reuse this same tailored resume.

- Derive the name from the 2-3 most dominant technical keywords/category from Step 3 (e.g. `python-backend-aws`, `react-frontend`, `data-pipeline-etl`). Kebab-case, at most 4 words.
- Before creating a new folder, check `<resume-root>` for an existing folder whose name matches or closely overlaps this category. If one exists, tell the user and ask whether to reuse/update that folder or create a new variant (append `-2`, `-3`, etc.) rather than silently overwriting or duplicating.
- If no category can be confidently derived, fall back to a generic label plus a date disambiguator (e.g. `general-2026-07`).

## Step 10 — Write & compile

1. `mkdir -p <resume-root>/<folder>`
2. Write the tailored LaTeX to `<resume-root>/<folder>/resume.tex` — a brand-new file in the new folder. Do not write to, overwrite, or copy over `<resume-root>/resume.tex`; that file must be byte-for-byte unchanged at the end of this run.
3. Run `tectonic resume.tex` from inside that folder (e.g. `cd <resume-root>/<folder> && tectonic resume.tex`)
4. Capture whether it succeeded, and the full tectonic output either way.

## Step 11 — Log to APPLICATIONS.md

Read `<resume-root>/APPLICATIONS.md`. If it doesn't exist, create it with this header first:

```markdown
# Application History

Tracks every tailored resume generated by /customize-resume.
Read this before tailoring a new resume to avoid contradicting past phrasing/claims,
and to spot an existing folder for the same keyword category.

| Date | Folder | Category | Company | Role | Keywords Added/Emphasized | Status |
|------|--------|----------|---------|------|----------------------------|--------|
```

Append one row for this run — even if the compile failed (use `Failed: <short reason>` as the Status in that case, otherwise `Compiled`). Use `Unknown` for Company/Role if the JD didn't specify them — never leave a cell blank. "Category" is the keyword/category the folder is named after (from Step 9). "Keywords Added/Emphasized" should list only what was actually woven into this resume (semicolon-separated), not the full keyword list from Step 3.

## Step 12 — Report to the user

Present: the JD summary, the categorized keyword list, which keywords were woven in vs. not reflected, the new folder path, the compile result (success + PDF path, or the raw tectonic error), and the row added to `APPLICATIONS.md`.
