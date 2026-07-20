---
description: Write a fresh cover letter for a job description, drawing facts from the master resume and layout from the master cover letter, then compile it to PDF with tectonic.
argument-hint: "[paste the job description, or a path to a file containing it] [--resume <path>]"
allowed-tools: Read, Write, Glob, Bash(mkdir *), Bash(mktemp *), Bash(tectonic *), Bash(cp *), Bash(rm *), Bash(ls *), Bash(grep *), Bash(wc *), Bash(git *)
disallowed-tools: Edit
disable-model-invocation: true
---

# Write a Cover Letter for a Job Description

This command works from wherever it's invoked. It has **two** master inputs, and they serve different jobs:

| File | Role | What it provides |
|---|---|---|
| `resume.tex` | **Source of truth** | Every fact, employer, skill, project, and number the letter may use |
| `cover-letter.tex` | **Template only** | Layout, preamble, contact header, salutation, sign-off |

The distinction matters and is easy to get wrong: `cover-letter.tex` is a **shape, not a story**. Its body prose was written for a different role. Do not mine it for content, do not carry its claims forward, and do not treat its emphasis as a starting point. Every run writes a genuinely new letter from the resume. A letter assembled by rewording the last one drifts further from the truth with each application and drags stale, irrelevant framing along with it.

This skill is standalone — it doesn't read from or depend on any other plugin's files.

## Step 0 — Locate the two masters

**The resume (source of truth).**

- If `$ARGUMENTS` contains `--resume <path>`, use that file. This is how the user points at a specific version, and it always wins.
- Otherwise search for `resume.tex`: the current working directory first, then recursively from it (`Glob **/resume.tex`).
- If exactly one match is found, use it. If none or several are found, ask the user which resume to use and stop until they reply.
- Call the containing directory `<root>` for the rest of this run.

**The cover letter (template).**

- Look for `cover-letter.tex` in `<root>`.
- If it's missing, bootstrap one from the resume: read the contact details out of `resume.tex` and write a minimal, self-contained `article`-class layout with a header block (name + contact line), a `\today` date line, `Dear Hiring Manager,`, three short placeholder body paragraphs, and a sign-off block. Ask the user only for what the resume doesn't already carry, in one message: how they like to sign off (e.g. "Sincerely, Name"), and anything missing from the contact header.
- Keep the placeholder body deliberately generic. It is scaffolding to be replaced on every run, not a draft to be edited. Do not write real accomplishments into it.

Both files are **read-only** for the whole run. Neither is ever written to or overwritten.

## Step 1 — Get the job description

- Strip any `--resume <path>` flag from `$ARGUMENTS` first; the remainder is the JD input.
- If the remainder is empty, ask the user to paste the job description now and stop until they reply.
- If it looks like a file path, read that file as the JD text.
- Otherwise, treat it verbatim as the JD text.

## Step 2 — Read both masters

Read `resume.tex` in full. This is the only place facts may come from.

Read `cover-letter.tex` and take from it **only**:

- The LaTeX preamble and packages, reproduced exactly.
- The contact header block, date line, and salutation, reproduced exactly.
- The sign-off and signature block, reproduced exactly.
- The paragraph count and rough shape of the body.

Everything between the salutation and the sign-off is discarded and written fresh in Step 5. Do not read its claims into this run's letter, even where they seem to fit.

## Step 3 — Guardrails

- Every fact in the letter must be traceable to `resume.tex`. Never invent a skill, employer, accomplishment, or metric, and never carry one over from the template's placeholder prose.
- Never write to or overwrite `resume.tex` or `cover-letter.tex`.
- Reproduce the structural blocks (Step 2) exactly. Only the body between salutation and sign-off is authored.
- Escape LaTeX special characters (`% & $ # _ { } ~ ^ \`) in any authored prose.
- If a JD requirement has no truthful basis in the resume, don't force it in. Note it in the final report as "not addressed — no matching experience."

## Step 4 — Gap analysis

Before writing a word, map the JD against the resume.

1. Pull the JD's requirements and rank them: what the role is *actually* built around (usually 3-5 things, often stated first or repeated) versus boilerplate every posting carries.
2. For each top-ranked requirement, find the evidence for it in `resume.tex` and note it. If there is none, mark it unaddressed. Do not reach for an adjacent-sounding accomplishment to cover the hole.
3. Pick the **two or three** strongest matches. These are the letter. Everything else in the resume stays out of this run, however good it is.

The resume is a superset: a reservoir to draw from, not a checklist to drain. A letter that lands three points well beats one that gestures at eight.

Because this analysis runs fresh against each JD, two applications should produce visibly different letters. If the draft is converging on the same three points every time regardless of the role, the ranking in step 1 was not done honestly.

## Step 5 — Write the letter

Author the body from scratch, using the Step 4 matches and the Step 3 guardrails. Hold the draft in memory.

### Shape

Three paragraphs, **250-400 words total**, one page. Never more.

- **Opening (2-3 sentences).** Name the role and say the specific thing that draws you to this one. Lead with the single strongest match from Step 4. Not a windup, not a summary of the whole letter.
- **Body (1-2 paragraphs).** One match per paragraph. State what you did, then what came of it. Concrete beats broad: one real project explained plainly outperforms three name-dropped in a list.
- **Closing (2-3 sentences).** What you want to work on there, and a plain closing line. No restating the letter.

### Using numbers

Include a metric only when the JD asks for that dimension of the work. A latency number belongs in a letter for a performance-critical role; in a letter for a design-systems role it is noise, and reads as padding lifted straight from the resume.

Cap: **two metrics in the whole letter.** If a fact is compelling without a number, leave the number off. The resume already lists the numbers. The letter carries the reasoning the resume can't.

### Sounding like a person

Write the way the user would explain the job to a smart friend: plain, direct, warm, specific. The test is whether a person reading it would guess a human wrote it. Read every sentence back and ask that question honestly.

**Dashes.** Do not use em dashes or en dashes anywhere in the letter. Not `—`, not `–`, not the LaTeX ligatures `---` or `--`. This is the single most common tell and it is non-negotiable. Where a dash feels natural, the sentence wants one of these instead:

- A full stop. Two clear sentences almost always beat one spliced sentence.
- A comma, where the aside is short.
- A colon, where the second half explains the first.
- Parentheses, sparingly, where the aside is genuinely optional.

Ordinary hyphens inside compound words are fine and expected (`design-systems work`, `well-tested`). What is banned is the dash used as a dramatic pause.

**Other tells to avoid:**

- Inflated register where a plain word works: *leverage, spearhead, orchestrate, utilize, delve, robust, seamless, pivotal, meticulous, passionate, dynamic, synergy, holistic.*
- Stock openers and cadences: "I am excited to apply", "As a seasoned professional", "Not only X, but Y", "This role is the perfect opportunity to", "I would welcome the chance to leverage".
- Rule-of-three lists that exist for rhythm rather than content. Real writing rarely produces three balanced items in a row.
- Every sentence opening with a participial windup: "Having led…", "Leveraging my background in…", "Drawing on…". One is fine. Three is a signature.
- Connective padding: *moreover, furthermore, additionally, it is worth noting that.*
- Defensive or apologetic framing: "Although I lack direct experience in…", "While my background may not perfectly align…". Unaddressed requirements go in the Step 9 report, not into the letter as an apology.
- Flattery any applicant could have written ("your industry-leading platform"). If the praise isn't grounded in something specific in the JD, cut it.

**What helps:** contractions where natural. Sentences of uneven length. Concrete nouns. One idea per sentence. The shorter word over the longer one.

## Step 6 — Quality gates

Read the draft back and check it. If any gate fails, revise and re-check. Do not compile a draft that fails a gate.

- 250-400 words, three paragraphs, one page.
- Zero em dashes and en dashes, in any form.
- At most two metrics, each one earned by a JD requirement.
- Every fact traceable to `resume.tex`. Nothing carried over from the template body.
- The opening sentence could not be pasted into an application for a different role. If it could, rewrite it.
- No sentence exists only to sound impressive. Cut it and see if the letter is worse; if it isn't, leave it cut.
- No apologetic or defensive sentence anywhere.
- Read aloud, it sounds like the user talking, not like a template.

## Step 7 — Name the PDF, verify, compile, and keep only the PDF

The tailored `.tex` is a build intermediate, not an artifact. Only the compiled PDF is kept. Every run produces its own PDF directly inside `<root>/cover-letters/` (no per-job subfolder), named after the **organization and designation** being applied to:

- Filename: `<organization>-<designation>.pdf`, kebab-case (e.g. `stripe-senior-backend-engineer.pdf`). Derive both from the JD; if the JD doesn't state the organization or the designation, ask the user for the missing one(s) before compiling. Don't guess and don't fall back to a generic name.
- If a PDF with that exact name already exists in `<root>/cover-letters/`, tell the user and ask whether to overwrite it or add a disambiguator (e.g. `-2026-07`), rather than silently replacing it.

Then:

1. `mktemp -d` to get a scratch directory, and write the letter there as `cover-letter.tex`.
2. **Verify the dash rule mechanically** — self-inspection is not enough, and this is the failure the user cares most about. Run `grep -n -e '—' -e '–' -e '--' <scratch>/cover-letter.tex`. Every hit must be inside the preamble or a LaTeX command carried over from the template. A hit anywhere in the authored body is a bug: rewrite the sentence, write the file again, and re-run the check until the body is clean.
3. Check the length: `wc -w <scratch>/cover-letter.tex` as a rough guide, with the authored body counted properly by eye against the 250-400 range.
4. `cd` into the scratch directory and run `tectonic cover-letter.tex`.
5. On success: `mkdir -p <root>/cover-letters` and `cp cover-letter.pdf <root>/cover-letters/<organization>-<designation>.pdf`.
6. Remove the scratch directory (`rm -rf` on the exact `mktemp` path only, never a wildcard or any other path).
7. On failure, write nothing into `<root>/cover-letters/`. Report the raw tectonic error instead.

## Step 8 — Push to GitHub for record keeping (optional, confirmation required)

Only if the compile succeeded:

- Check whether `<root>` is inside a git work tree with a GitHub remote: `git -C <root> rev-parse --is-inside-work-tree`, then `git -C <root> remote get-url origin`. If either check fails or the remote isn't a github.com URL, skip this step silently and go to Step 9.
- If linked, ask the user once: push this run's output to GitHub for record keeping? If they decline, skip to Step 9.
- On yes, stage **only this run's files**: `<root>/cover-letters/<organization>-<designation>.pdf`, plus `<root>/cover-letter.tex` only if it was bootstrapped this run (Step 0). Never `git add -A` or unrelated changes. Commit with a short message like `cover letter: <organization> <designation>`, then `git push` to the current branch. If the push fails (auth, diverged branch), report the raw error and move on. Never force-push or mutate history.
- Build the **blob link** to the PDF and include it in the Step 9 report:
  1. Origin URL → `https://github.com/<owner>/<repo>` (convert SSH form `git@github.com:<owner>/<repo>.git` and strip any trailing `.git`).
  2. Branch: `git -C <root> rev-parse --abbrev-ref HEAD`.
  3. Repo-relative PDF path: `git -C <root> ls-files --full-name "cover-letters/<organization>-<designation>.pdf"`.
  4. Link: `<repo-url>/blob/<branch>/<repo-relative-pdf-path>` (URL-encode the path segments if needed).

Each application gets its own PDF and therefore its own permanent blob link. The record per application never gets overwritten by the next run.

## Step 9 — Report to the user

Present: a short JD summary (role, company), which resume evidence was selected and why, which requirements weren't addressed (if any), the final PDF path or the raw tectonic error if compilation failed, and, if Step 8 pushed, the GitHub blob link to the PDF (or a note that the push was skipped or failed, and why).
