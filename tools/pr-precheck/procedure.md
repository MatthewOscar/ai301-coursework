# Procedure: how this tool grades a PR package

Follow these steps in order, exactly as written. Record facts before grading anything. Steps marked 'Live only' are the live-mode branch, and eval mode skips them. Every other step runs in both modes.

## Read order

1. Pick the mode.
   - Eval: you were given a package bundle. It is the whole world: fetch nothing, and ignore scope.md and voice-guide.md.
   - Live: you were given the author's working copy, plan.md, pr_draft.md, test_evidence.md, and an issue URL. A house-chain student uses the house plan and the house repro pack.
2. Live only, before anything else: read scope.md.
   - (a) If its repo line is still a bracketed placeholder, stop. Reply with a plain message telling the user to get the cohort's scope file from the instructor. Give no grades and no JSON block.
   - (b) If the working copy or the issue is in any other repo, refuse the same way and say the work is outside the scoped repo.
   - (c) Note any house rules, and apply them while gathering.
3. Live only: read voice-guide.md and note its rules for the summary. They never change a grade or the verdict. If the file is still template text, say so and continue.
4. Read the repo facts and the issue, and record:
   - each template section and ask
   - each contribution ask (changelog, issue link, tests)
   - each stated check (suite, formatter, linter, generator, pre-commit)
   - the AI policy wording, verbatim, with the stage it names
   - the reported behavior, the repro, and any explicit maintainer direction in the thread

   Eval: read the Repo facts, Issue, and Thread highlights sections. Live: run `gh issue view <url> --comments`. Then read the PR template, CONTRIBUTING, and any AI policy file via `gh api repos/<owner>/<repo>/contents/<path>` or the web, read-only. Record a failed retrieval as 'unavailable', never as absence. Record 'no stated policy' only when the policy was read and says nothing.
5. Read the plan before the diff: its scope, Files list, not-in-scope line, deviation and limit notes, repro evidence, and test plan. Eval: the Plan context block. Live: plan.md. Record each named file, each named deliverable, each test-plan item, and each recorded deviation or deferral.
6. Read the diff and commits. Eval: the Candidate PR's Diff and Commits. Live: run `git diff main...HEAD` and `git log main..HEAD` from the working copy, substituting the default branch if it is not main. Read every hunk in every file, tests and changelog included.
7. Read the test evidence. Eval: the Test evidence section. Live: test_evidence.md.
8. Read the description last. Eval: the Title and Description. Live: pr_draft.md, where the first line is the title and the rest is the description.

Why this order: the repo facts and the plan fix what was asked and promised. The diff and the evidence fix what was done. The description is then checked against facts already established, so its confidence or checklist cannot set the frame.

## Evidence gathering

Build these records before grading.

1. File map (diff against plan). Label each changed file with one of these:
   - planned: the plan names it, or it is a helper inside a planned file
   - companion: a test, changelog, release note, translation, or generated file that serves the fix
   - recorded deviation: a plan note records it
   - unplanned

   In planned files, note any hunk whose behavior the plan does not name (an option, flag, setting, API, rename, rewrite, version bump, or second fix). List each plan deliverable that no hunk delivers, and whether the plan or description says it was dropped or deferred.
2. Test-plan map (plan against evidence). First, record the reported behavior's before (from the evidence, the plan's repro, or the issue) and its after (captured output or the concrete observed result of the repro step). If there is no proper before and after, record which case applies instead: 'none', 'unchanged path only', 'prose only', or 'admitted untested'.

   Then label each test-plan item with one of these:
   - shown: there is output for it
   - stated: it is named with an outcome, such as 'control unchanged'
   - covered by an added test: the test runs that item's input, and the suite run is shown
   - deferred: the plan or description defers it with a reason
   - absent
3. Repo checks: for each check the repo facts, contribution docs, or test plan state, quote its outcome line, or record 'no outcome'.
4. Debris list: quote every debug print, piece of commented-out code, dead helper or allow-dead-code shim, stray TODO, and churn hunk. Churn means imports, reformatting, re-indenting, identical lines re-added outside an edited statement, and drive-by edits.
5. Claims list: quote each description claim about the contents, and mark whether the diff supports it.
6. Standards list: for each required template section or ask, record the description content or diff file that answers it, or record 'missing'. Quote any AI disclosure sentence, or record 'none'.

When a fact is genuinely absent, record 'not found', or 'unavailable' for a failed live retrieval. Never guess a fact. Step 2 of Check execution says what each absence means.

## Check execution

1. Grade in rubric order: diff-matches-plan, claims-match-diff, fix-shown-before-after, test-plan-covered, repo-checks-run, diff-reviewable, template-asks-met, ai-disclosure-met.
2. For each check, read its Not-failures list first, then test each lettered fail limb against the recorded facts. A case the Not-failures list describes passes. A disqualifying fact that is simply absent passes (for example, no debris, or no stated AI policy). The exception is the three test rows: there, a test-plan item, repro, or stated check with no recorded output is a quoted absence and fails.
3. A deferral or limit that the plan records and the description restates is bounded: it is neither drift nor untested. It never excuses a reported behavior that is not shown. A description's admission of an unplanned change does not re-tie it; only a plan deviation note does.
4. Grade from the recorded facts. Re-read a package part only when a recorded fact looks incomplete, and then re-read only that part.
5. Grade unclear only when the facts leave real doubt about whether a fail limb applies. Never use it to dodge a call. Never grade on length, polish, tone, heading style, or commit count.
6. Grade and report every check, even after one fails.
7. If a step you need is not covered here, report the gap in the summary and use the narrowest reading the rubric allows.

## Verdict assembly

1. Map each unclear grade to pass or fail using the rubric's verdict rule.
2. If any check is a fail, or an unclear counted as fail, the verdict is reject. Otherwise it is accept. There is no third outcome.
3. The deciding check is the first check in rubric order whose grade counts as fail. On a reject, name it and quote its deciding fact.
4. Each evidence line is one line: the quote or fact that decided the grade, plus the limb that fired for a fail.
5. Write the reply in this order:
   - (a) Live only: any retrieval marked unavailable.
   - (b) One line per check (name, grade, deciding fact), then 'Deciding check: <name>' on a reject.
   - (c) Live only: each voice-guide rule the title or description breaks, quoted and marked as not affecting the verdict. Then any procedure gaps.
   - (d) Exactly one fenced json block in SKILL.md's schema, listing all eight checks in rubric order, with nothing after it. Set item to the bundle id in eval mode. In live mode, use the PR URL if one exists, or else a stable draft id such as the issue reference plus 'draft'.
