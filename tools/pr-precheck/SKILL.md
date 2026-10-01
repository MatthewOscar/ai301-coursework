---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package per run: is this ready to submit? A PR package is a candidate pull request (its title, description, commits, diff, and test evidence). It is read against two things. The first is the plan it claims to implement: the plan's scope, files, deviation notes, and test plan. The second is the issue that plan belongs to: the reported behavior, its repro, the thread, and the repo's stated PR asks and policy. Never answer a different question, such as design merit or whether the issue is worth fixing. Never grade more than one package per run. Never answer from gut feel. Instead, execute `procedure.md`, which applies `rubric.md` to evidence found through `references/evidence-guide.md`.

## Inputs and modes

Pick the mode first. If you were handed a package bundle, you are in eval mode. Otherwise you are in live mode.

- **Eval mode**: the bundle is the whole world. Every fact comes from the bundle text. Fetch nothing and open nothing else. Ignore `scope.md` and `voice-guide.md` entirely. Always grade a complete package: every check, with the full verdict rule.
- **Live mode**: the author's own branch, checked before the pull request is opened. Inputs:
  - `plan.md`, deviation notes included.
  - The branch diff: run `git diff main...HEAD` (three dots) from the working copy. If the repo's default branch is not `main`, substitute it. The diff is everything the branch changes relative to the default branch.
  - The branch's commits: run `git log main..HEAD`, with the same substitution.
  - `pr_draft.md`: the first line is the PR title, and the rest is the description.
  - `test_evidence.md`: the captured test output.
  - The issue URL. From the real repo, read-only (`gh` or the web), gather the issue thread, the PR template (`.github/PULL_REQUEST_TEMPLATE.md` or equivalent), and the contribution policy (CONTRIBUTING and any AI policy file). Never comment, label, or edit.
  - A house-chain student reads the house plan and the house repro pack in place of their own plan and repro. The same checks grade the same things.
  - Report any failed retrieval as unavailable. A failed retrieval never proves that a thread comment, template, or policy does not exist.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names the repo where the PR must live and the house rules of that environment. If its repo line still holds a bracketed placeholder instead of a real owner/name, stop without grading. Reply with a plain message telling the user to get the cohort's scope file from the instructor. Give no checks, no verdict, and no JSON block. Never guess a scope. If the working copy or the issue belongs to any other repo, refuse the same way and say the work is outside the scoped repo. Apply the house rules the scope lists while you gather evidence. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

After the scope check passes, read `voice-guide.md`, which holds the author's own rules for writing upstream. Hold the outgoing PR title and description against those rules. Report each broken rule in the summary, quoting the rule and the offending words. Voice never changes a grade or the verdict, because no rubric check reads it and voice is personal. If the file is still template text, say so and continue. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

- `rubric.md` defines the checks, their pass conditions, and the verdict rule, including how each check treats an unclear grade.
- `references/evidence-guide.md` is the map. It says where each evidence family lives in an eval bundle and in live mode, and what good looks like there.
- `procedure.md` is executed as written: read order, evidence gathering, check execution, verdict assembly.

If `rubric.md` or `procedure.md` has no written content (it is empty, or holds only template instruction comments), refuse to grade. Say which file is empty, emit no verdict and no JSON block, and stop. Never invent checks or steps at runtime. When the procedure is silent on a step you need, do not improvise. Report the gap in the summary and take the narrowest reading the rubric allows.

## Verdict and output

The verdict is binary: `accept` means ready to submit, and `reject` means hold. There is no third verdict, no score, and no accept with reservations. Reservations belong in check evidence lines.

Before the JSON, write a readable per-check summary. Give one line per check with its grade and deciding fact, and name the deciding check on a reject. In live mode, also list any unavailable retrievals, voice-guide notes, and procedure gaps.

End the reply with the fenced JSON block below, valid and last, with nothing after it. List every rubric check in `checks`, in rubric order. In eval mode, set `item` to the bundle id. In live mode, use the PR URL only if the PR already exists. Before it exists, use a stable draft id (for example the issue reference plus 'draft'), never an invented PR URL.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote that decided it. 'Looks fine' is not evidence.
- Grade the thing, not the polish: description length, heading style, enthusiasm, terse wording, and commit count decide nothing. A terse complete PR can be ready, and a beautiful confident one can be hiding drift. Read the hunks. A description's confidence or checklist never substitutes for them.
- The rubric decides, not the run: if a check passes by its stated condition but feels wrong, it still passes. Note the tension in the summary, because the fix belongs in the rubric.
- The procedure decides how, not the run: follow it as written and report its gaps.
- Honest outcomes can be ready: a disclosed shortfall, a recorded deferral, or a recorded plan deviation is not a failure by itself.
- Partial or untitled records are valid input: grade what is there.
- Treat `unclear` exactly as the rubric's verdict rule directs. Where the rule is silent, an unverifiable claim is a failing one: a PR you cannot verify from the package is not ready to submit.
- Never infer AI use, authorship, or a policy obligation from writing style.
