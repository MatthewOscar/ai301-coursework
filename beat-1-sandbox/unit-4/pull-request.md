# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

I am enrolled in two Path Review sections, so I opened one pull request per section, each from that section's fork and each citing only its own runs:

- Section 1: https://github.com/codepath/pathreview-ai301-fa26-s1/pull/81
- Section 3: https://github.com/codepath/pathreview-ai301-fa26-s3/pull/84

**Branch**

`fix/22-github-tool-integration`

The same branch name on both forks: `MatthewOscar/pathreview-ai301-fa26-s1` (head `dc6a129`) and `MatthewOscar/pathreview-ai301-fa26-s3` (head `70dc681`). Both pull requests fix issue #22 in their section.

## Eval iterations

**Run history**

1. B0, a blind dry run, not a harness run: the first synthesis (built from the Claude candidate alone, because the Codex author hit a usage limit) rendered through the harness prompt for all 24 bundles and graded by Sonnet with no tools and no answer key in reach. 20/20 scored, 4/4 calibration. Superseded once a direct Codex retry succeeded and the final design merged both candidates.
2. B1, a blind dry run of the installed final tool, same method, not a harness run: 20/20 scored, 4/4 calibration, every category matched.
3. Run 1, harness smoke (`--limit 3`): `agreement: 3/3 scored items`.
4. Run 2, harness full run with `--save-run`, the run committed as `eval-run.txt`: `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3` and `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

No package disagreed in Run 2, so there was no `--only` re-run and no revision after it.

**Package analysis**

`pkg-13`, a clear-accept. Gold label: accept. My tool's verdict in Run 2: accept, with all eight checks passing.

The package is a fix that caret-escapes cmd metacharacters on Windows and deliberately leaves `%VAR%` expansion alone. The plan says so before any code exists ("Known limit, stated in the plan: `%` ... is NOT covered by this change"), and the description repeats it ("percent-sign environment expansion (`%VAR%`) is NOT handled here ... this description restates it so reviewers can weigh the limit"). A rubric that reads "less than everything" as "hold" rejects this package, and the gold note calls it arguable for exactly that reason.

My rubric reads it as ready because of how the rows are built. The rubric's honest-outcome rule says "A deferral that the plan records and the description restates is bounded: it is neither drift nor untested." Under `diff-matches-plan`, "a deferral or limit the plan records" is a listed Not-failure, and the grader's evidence line shows it used that: "`%` is left out per the plan's recorded limit." Under `test-plan-covered`, the repro is shown and the plan's control only appears as the statement "No-ampersand control unchanged". That passes because the row counts an item as covered when "the evidence names it with an outcome (captured output, or a statement such as 'control unchanged')". The grader's line: "Repro and exit code shown, 'No-ampersand control unchanged' stated, ui functional spec run shown passing." So the tool accepted it for the reason the gold label does: the shortfall is real, recorded before the build, and told to the reviewer, and the slice that shipped is proven.

**Check rationale**

The check, quoted from `tools/pr-precheck/rubric.md` as uploaded:

> | test-plan-covered | The plan's test-plan items (each named repro, failure mode, control, platform case, and docs or render check), listed against the test evidence and the tests the diff adds. | Pass unless you can quote a test-plan item that the evidence never mentions, that no test added in the diff covers, and that neither the plan nor the description defers with a reason. An item counts as covered in either of two cases: the evidence names it with an outcome (captured output, or a statement such as 'control unchanged'), or an added test runs that item's input and the suite run is shown. An item is not covered because it shares code with a shown item. If the evidence and the added tests are silent on it, it is absent. Not failures: a plan with no test plan; an item the plan or description honestly defers with a reason; terse wording. | required |

The Claude candidate's version of this row was looser in two places. It listed "a regression control the plan lists but the evidence does not repeat, when the reported behavior's before and after are shown" as a Not-failure, and its verdict rule counted `unclear` as a pass for this check. Both critics predicted the candidate would still score well, but the synthesis read the calibration case where a plan names two failure modes and the evidence re-runs only one: under the old wording a grader could decide the second mode "shares the same code" and wave it through. So the final row defines what covered means (a named outcome, or an added test whose run is shown), says outright that sharing code with a shown item is not coverage, and the verdict rule now counts `unclear` as a fail here, because the row asks whether the package proves its claim.

What I rejected was the control exemption itself. A control the plan names is part of the promise, and dropping it silently is the same failure as dropping a failure mode. The cost of that choice is the statement clause: a terse PR that only writes "control unchanged" has to pass, or every honest one-line control would be held. That clause is why `pkg-13`, `pkg-16`, and `pkg-19` still accept.

**Trade-offs**

`test-plan-covered` with `unclear` counted as a fail gives up some safety on clear accepts. The synthesis flagged `pkg-13`, `pkg-16`, and `pkg-19` as the packages most likely to flip, because their controls are stated rather than captured. I kept the rule and checked those three instead of loosening it: all three accepted in B1 and in Run 2.

`repo-checks-run` has no exemption for a check the diff cannot affect, and it cost me on my own PR. My first live run on section 1 held the draft because `docs/CONTRIBUTING.md` lists the frontend commands and my evidence said "Not run locally". Adding an "irrelevant to the diff" exemption would have been a judgment call the grader makes from the diff, which is exactly the kind of reading that drifts. So I ran `cd frontend && npm ci && npm test -- --run` on both branches (18 passed, exit 0) and kept the rule.

Nothing changed after Run 2, and I know nothing else moved because the four graded files were frozen at the hashes in the `eval-run.txt` header, and every scored package agreed, so no canary was needed.

## My pr-precheck verdict

I ran `pr-precheck` in live mode on each section's draft before opening it (title and description in `pr_draft.md`, the diff from `git diff main...HEAD`, my plan, and `test_evidence.md`).

- Section 1: the first run held it (reject) on `repo-checks-run`: "CONTRIBUTING.md lists 'cd frontend && npm ci && npm test -- --run' ... no outcome line exists." After I ran the frontend commands and added their output, the next run accepted. One more run after pasting the mutation-check output into the description also accepted, with all eight checks passing. That draft is what I opened.
- Section 3: accepted on the first run, and again after the same mutation-output edit, all eight checks passing.

After I opened them, GitHub Actions ran on both pull requests and all five jobs passed in each (`lint`, `typecheck`, `test-unit`, `test-integration`, `frontend`), the first run of the new integration step outside my machine.

The only voice-guide note left on both is the em dash inside the template's own "CI is green" checkbox line; it is the repo's text, so I kept it as written. I did not post plan comments on #22: the sandbox is a test environment, and I kept the issue threads clean for classmates working there.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
