---
description: Tailor a master cover letter to a job description and compile it to PDF with tectonic.
argument-hint: [paste the job description, or a path to a file containing it]
allowed-tools: Read, Write, Glob, Bash(mkdir *), Bash(mktemp *), Bash(tectonic *), Bash(cp *), Bash(rm *), Bash(ls *), Bash(git *)
disallowed-tools: Edit
disable-model-invocation: true
---

# Write a Cover Letter for a Job Description

This command works from wherever it's invoked. The master cover letter always lives at `<root>/cover-letter.tex`, where `<root>` is the current working directory. This skill is standalone — it doesn't read from or depend on any other plugin's files.

## Step 0 — Locate or create the master cover letter

- Look for `cover-letter.tex` in the current working directory (`<root>`).
- If it's missing, this is a first run — bootstrap one: ask for the truthful content needed to write it, in one message — full name and contact header (email, phone, city, LinkedIn/portfolio links); a 2-4 sentence career narrative / who-you-are; 3-6 key strengths or accomplishments to draw from later, with real numbers where the user has them; and how they like to sign off (e.g. "Sincerely, Name"). Write `<root>/cover-letter.tex` from their answers using a minimal, self-contained `article`-class layout: a header block (name + contact line), a `\today` date line, a `Dear Hiring Manager,` salutation, 2-3 body paragraphs built from the narrative and strengths given, and a sign-off block.
- Write the body paragraphs in the voice described in Step 5, and record the user's strengths in their own words. The master is the reservoir every future run draws from, so a master written in inflated language poisons every letter after it. Length is the one rule that differs: the master may run long, since each run selects a subset from it.
- Tell the user this file is their durable master. It is read-only for every later step and every future run — never overwritten directly.

## Step 1 — Get the job description

- If `$ARGUMENTS` is empty, ask the user to paste the job description now and stop until they reply.
- If `$ARGUMENTS` looks like a file path, read that file as the JD text.
- Otherwise, treat `$ARGUMENTS` verbatim as the JD text.

## Step 2 — Read the master and identify what's editable

Read `<root>/cover-letter.tex`. It is **read-only**: only ever read, never written or edited.

Editable: the opening hook sentence, the body paragraph(s) connecting the user's background to the role, and the closing line before sign-off.
Off-limits: the LaTeX preamble/packages, the contact header block, the date line, the salutation, and the sign-off/signature block — preserve these exactly as-is.

## Step 3 — Guardrails

- Never fabricate a skill, employer, accomplishment, or metric that isn't already present in `cover-letter.tex`.
- Never write to or overwrite `<root>/cover-letter.tex` itself.
- Only reword the editable sections (Step 2); never touch the off-limits ones.
- Escape LaTeX special characters (`% & $ # _ { } ~ ^ \`) in any new or edited prose.
- If a JD requirement has no truthful basis anywhere in the master letter, don't force it in — note it in the final report as "not addressed — no matching experience."

## Step 4 — Gap analysis

Before writing a word, map the JD against the master letter.

1. Pull the JD's requirements and rank them: what the role is *actually* built around (usually 3-5 things, often the ones stated first or repeated) versus boilerplate every posting carries.
2. For each top-ranked requirement, find the evidence for it in `cover-letter.tex` and note it. If there is none, mark it unaddressed — do not reach for an adjacent-sounding accomplishment to cover the hole.
3. Pick the **two or three** strongest matches. These are the letter. Everything else in the master stays out of this run, however good it is.

The master is a superset — a reservoir to draw from, not a checklist to drain. A letter that lands three points well beats one that gestures at eight.

## Step 5 — Draft the tailored letter

Reword the editable sections around the matches from Step 4, per the Step 3 guardrails. Hold this draft in memory.

### Shape

Three paragraphs, **250-400 words total**, one page. Never more.

- **Opening (2-3 sentences).** Name the role and say the specific thing that draws you to this one. Lead with the single strongest match from Step 4. Not a windup, not a summary of the whole letter.
- **Body (1-2 paragraphs).** One match per paragraph. State what you did, then what came of it. Concrete beats broad: one real project explained plainly outperforms three name-dropped in a list.
- **Closing (2-3 sentences).** What you want to work on there, and a plain sign-off line. No restating the letter.

### Using numbers

Include a metric only when the JD asks for that dimension of the work. A latency number belongs in a letter for a performance-critical role; in a letter for a design-systems role it is noise, and reads as padding lifted from a resume.

Cap: **two metrics in the whole letter.** If a fact is compelling without a number, leave the number off. A cover letter's job is the reasoning the resume can't carry — the resume already lists the numbers.

### Voice

Write the way the user would explain the job to a smart friend: plain, direct, warm, specific.

Do not use:

- **Em dashes and en dashes** (`—`, `–`). Use a period, comma, or colon. In LaTeX this also means no `---` or `--` in prose.
- Inflated register where a plain word works: *leverage, spearhead, orchestrate, utilize, delve, robust, seamless, pivotal, meticulous, passionate, dynamic, synergy, holistic.*
- The AI cadences: "I am excited to apply", "As a seasoned professional", "Not only X, but Y", "This role is the perfect opportunity to", "I would welcome the chance to leverage", rule-of-three lists that exist for rhythm rather than content.
- Defensive or apologetic framing: "Although I lack direct experience in…", "While my background may not perfectly align…". Unaddressed requirements go in the Step 9 report, not into the letter as an apology.
- Flattery of the company that any applicant could have written ("your industry-leading platform"). If the praise isn't grounded in something specific in the JD, cut it.

One idea per sentence. Vary sentence length. Prefer the shorter word.

## Step 6 — Quality gates

Read the draft back and check it before compiling. If any gate fails, revise and re-check. Do not compile a draft that fails a gate.

- 250-400 words, three paragraphs, one page.
- Zero em dashes or en dashes in the prose.
- At most two metrics, each one earned by a JD requirement.
- Every claim traceable to `cover-letter.tex`. No new employer, skill, accomplishment, or number.
- The opening sentence could not be pasted into an application for a different role. If it could, rewrite it.
- No sentence exists only to sound impressive. Cut it and see if the letter is worse; if it isn't, leave it cut.
- No apologetic or defensive sentence anywhere.

## Step 7 — Name the PDF, compile, and keep only the PDF

The tailored `.tex` is a build intermediate, not an artifact — only the compiled PDF is kept. Every run produces its own PDF directly inside `<root>/cover-letters/` (no per-job subfolder), named after the **organization and designation** being applied to:

- Filename: `<organization>-<designation>.pdf`, kebab-case (e.g. `stripe-senior-backend-engineer.pdf`). Derive both from the JD; if the JD doesn't state the organization or the designation, ask the user for the missing one(s) before compiling — don't guess and don't fall back to a generic name.
- If a PDF with that exact name already exists in `<root>/cover-letters/`, tell the user and ask whether to overwrite it or add a disambiguator (e.g. `-2026-07`), rather than silently replacing it.

Then:

1. `mktemp -d` to get a scratch directory, and write the tailored LaTeX there as `cover-letter.tex`.
2. `cd` into the scratch directory and run `tectonic cover-letter.tex`.
3. On success: `mkdir -p <root>/cover-letters` and `cp cover-letter.pdf <root>/cover-letters/<organization>-<designation>.pdf`.
4. Remove the scratch directory (`rm -rf` on the exact `mktemp` path only — never a wildcard or any other path).
5. On failure, write nothing into `<root>/cover-letters/` — report the raw tectonic error instead.

## Step 8 — Push to GitHub for record keeping (optional, confirmation required)

Only if the compile succeeded:

- Check whether `<root>` is inside a git work tree with a GitHub remote: `git -C <root> rev-parse --is-inside-work-tree`, then `git -C <root> remote get-url origin`. If either check fails or the remote isn't a github.com URL, skip this step silently and go to Step 9.
- If linked, ask the user once: push this run's output to GitHub for record keeping? If they decline, skip to Step 9.
- On yes, stage **only this run's files** — `<root>/cover-letters/<organization>-<designation>.pdf`, plus `<root>/cover-letter.tex` only if it was bootstrapped this run (Step 0) — never `git add -A` or unrelated changes. Commit with a short message like `cover letter: <organization> <designation>`, then `git push` to the current branch. If the push fails (auth, diverged branch), report the raw error and move on — never force-push or mutate history.
- Build the **blob link** to the PDF and include it in the Step 9 report:
  1. Origin URL → `https://github.com/<owner>/<repo>` (convert SSH form `git@github.com:<owner>/<repo>.git` and strip any trailing `.git`).
  2. Branch: `git -C <root> rev-parse --abbrev-ref HEAD`.
  3. Repo-relative PDF path: `git -C <root> ls-files --full-name "cover-letters/<organization>-<designation>.pdf"`.
  4. Link: `<repo-url>/blob/<branch>/<repo-relative-pdf-path>` (URL-encode the path segments if needed).

Each application gets its own PDF and therefore its own permanent blob link — the record per application never gets overwritten by the next run.

## Step 9 — Report to the user

Present: a short JD summary (role, company), which requirements weren't addressed (if any), the final PDF path — or the raw tectonic error if compilation failed — and, if Step 8 pushed, the GitHub blob link to the PDF (or a note that the push was skipped/failed and why).
