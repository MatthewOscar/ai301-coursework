# Procedure: how this skill grades a plan package

Follow these steps in order, exactly as written. Record each fact before grading anything.

## Read order

1. Pick the mode. Eval mode: you were given a package bundle. The bundle is the whole world: fetch nothing, and ignore scope.md and voice-guide.md. Live mode: you were given plan.md, a draft plan comment, and an issue URL. Skip every step marked "Live only" in eval mode.
2. Live only, before anything else: read scope.md.
   a. If its repo line still reads as a placeholder (bracketed stand-in tokens instead of a real owner/name pair; an HTML comment on that line does not count), stop without grading and without a verdict, and tell the student to get their cohort's scope file from the instructor. Never guess a scope.
   b. If the issue URL's owner and repo match none of the repos scope.md lists, refuse to grade: say the issue is outside the scoped source, and stop.
   c. Note scope.md's house rules; they govern how the thread is read. For example, a classmate's plan comment on the same issue neither blocks this plan nor counts as direction or in-flight work it must defer to.
3. Live only: read voice-guide.md and note its rules for the summary. They never change a grade or the verdict. If the file is still template text, say so in the summary and continue.
4. Read the issue: the reported behavior, its trigger, and the expected behavior. Eval: the Issue section. Live: `gh issue view <url> --comments` or the issue page, read-only (never comment, label, or edit). The issue comes first because it defines the behavior the repro steps measure.
5. Read the repro evidence before the thread and the plan. Eval: the Repro evidence section. Live: look in the thread for the student's own posted repro comment; if one exists, it is the repro evidence. If the thread holds none, use the repro evidence quoted in plan.md and the draft comment, and record "no posted repro comment; graded against the repro evidence quoted in the drafts" (if the drafts quote none, that absence is the diagnosis fact). Never treat an unposted draft as posted, and never open workspace files the drafts do not quote. Record every step's outcome, every control, contrast, and diagnostic run (what changed, what came out), and the earliest point where the defect appears. Why this order: the thread and the plan both make claims about the cause, and these runs are what every claim must fit. Reading the runs first keeps a confident thread comment or a polished plan from setting the frame.
6. Read the thread. Eval: the Thread highlights. Live: the full thread from step 4 plus linked PRs. Record each role-holder's direction (a named culprit, a chosen or rejected approach, a request to test) and every patch, test build, branch, or PR in flight on this behavior, with who opened it.
7. Read the repo policy. Eval: the Repo facts block. Live: the repo's CONTRIBUTING file, issue and PR templates, and any AI policy file, via `gh api repos/<owner>/<repo>/contents/<path>` or the web; if none exist, record "no stated policy". Copy the AI-use wording verbatim and note the stage it names (issue comments, all AI use in any form, or pull requests only).
8. Read the candidate plan and plan comment (live: plan.md and the draft comment) last, in full, as one package: a fact, deferral, or disclosure in either counts for both. They come last so each claim is read against facts already recorded.

## Evidence gathering

Record one fact per check, quoting where you can:

1. diagnosis-grounded: from the drafts, the stated cause in one sentence and the part it blames; from step 5, any run where the defect persists with that part absent, the part works under the plan's trigger conditions, or the defect appears before that part runs; from step 6, any role-holder cause the plan does not address.
2. scope-bounded: from the drafts, every proposed change (each list item and aside, not only the summary); which ones go beyond step 4's reported behavior without being deferred.
3. plan-executable: from the drafts, the chosen mechanism and every named file, function, module, or code path; any open fork or investigate-and-see phrasing, quoted.
4. test-plan-observable: from the drafts, the test-plan sentences verbatim, wherever they sit; the observable outcome they name, if any, and which repro step from step 5 it checks.
5. honesty-of-claims: from the drafts, each certainty sentence and whether steps 5 and 6 support it. Live only: if a draft points readers to a repro report "above" or "posted" that the thread does not contain, record that sentence here.
6. thread-aware: each direction or in-flight item from step 6 and whether either draft engages it.
7. repo-conventions-met: the AI-use wording and its stage from step 7; any disclosure sentence in either draft, quoted, or "none".

When a fact is genuinely absent (no thread comments, no control run, no stated policy), record "not found" and never guess one. Check execution step 4 says what each absence means.

## Check execution

1. Grade in table order: diagnosis-grounded, scope-bounded, plan-executable, test-plan-observable, honesty-of-claims, thread-aware, repo-conventions-met.
2. For each check, apply its rubric pass condition to the recorded fact: test each lettered fail limb, then its Not-failures list. A case the list describes passes.
3. Grade from the recorded fact alone. Re-read a package section only when the fact looks incomplete, and then only that section.
4. Absent evidence: a check whose disqualifying evidence is absent passes. The exceptions are plan-executable and test-plan-observable, where a plan with no approach, no named place, or no test outcome anywhere in either draft fails on that quoted absence. Short, partial, or untitled sections are valid input; never halt on them.
5. Grade unclear only when the fact leaves real doubt whether a fail limb applies, never to dodge a call the pass condition settles, and never on length, headings, tone, or polish.
6. Grade every check even after one fails.

## Verdict assembly

1. Map unclear by the rubric's rule: diagnosis-grounded, scope-bounded, honesty-of-claims, and thread-aware count as pass; plan-executable and test-plan-observable count as fail; repo-conventions-met counts as pass, and its limb (a) is never unclear.
2. If any check is fail, or unclear counted as fail, the verdict is reject. Otherwise it is accept.
3. Each check's evidence line quotes the deciding fact and, for a fail, names the limb that fired, for example "(a) ...".
4. Write the output in this order, with nothing else:
   a. Live only: one line naming the repro source (the posted comment, or its absence and what was used instead).
   b. One line per check: name, grade, deciding fact. On a reject, name the first failing check in table order as the primary reason.
   c. Live only: each voice-guide rule the draft comment breaks, quoted, marked as not affecting the verdict; then any procedure gap you hit.
   d. Exactly one fenced json block in SKILL.md's format, with "item" set to the bundle id (eval) or the issue URL (live), and nothing after it.
