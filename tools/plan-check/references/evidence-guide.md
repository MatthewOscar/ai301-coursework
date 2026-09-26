# Evidence guide: where evidence lives in a plan package

Eval mode reads only the bundle. Live mode reads plan.md and the draft plan comment, the issue and its thread (via gh or the web, read-only), and the repo's own policy files. It never opens workspace files the drafts do not quote, and never treats an unposted draft as posted. A role-holder is a thread participant labeled with a repo role (owner, member, collaborator, or contributor).

## Diagnosis and grounding

Where it lives: eval bundle, the Candidate plan's stated cause (a Diagnosis, Cause, or Summary line, or the approach's opening sentence when untitled), read against the Repro evidence section's steps and its control, contrast, and diagnostic runs, and against causes role-holders state in the Thread highlights. Live mode, plan.md's stated cause, read against the student's own posted repro comment on the issue; when the thread holds none, against the repro evidence quoted in plan.md and the draft comment (and the summary names that absence); and against role-holder comments in the live thread.

What good looks like: the stated cause predicts every run in the evidence, including the negative and surprising ones. No run shows the defect persisting with the blamed part absent, the blamed part working under the plan's own trigger conditions, or the defect already present before the blamed stage runs. A role-holder's more specific cause is adopted or answered on the record. A cause borrowed from the issue or the thread still has to fit the runs.

## Scope

Where it lives: eval bundle, every change the Candidate plan and Candidate plan comment propose (all list items and asides, plus any in-scope and not-in-scope lines), read against the Issue section's reported behavior. Live mode, plan.md's change list and scope lines and the draft comment, read against the live issue body.

What good looks like: one bounded change a maintainer could review in the time the reported bug deserves. Upgrades, migrations, new options, a second bug's fix, and redesigns are absent, or named and deferred. Auditing sibling sites of the same defect, regression tests, and docs for the fix are part of the fix. An honest scope-down (one platform or variant, with the rest named as deferred) is bounded, not incomplete.

## Executability

Where it lives: eval bundle, the Candidate plan's approach, change, and files or areas text, wherever it sits. Live mode, the same parts of plan.md.

What good looks like: a stranger could open the right place and start: a chosen mechanism plus a named file, function, module, or code path. Saying the exact function will be pinned during the build is fine once the code path and the mechanism are named. "Investigate and see", "which layer? not sure", and "whichever is easier" are not a plan.

## Test plan

Where it lives: eval bundle, the Candidate plan's test-plan text (titled or not), read against the Repro evidence steps and artifacts. Live mode, plan.md's test plan, read against the same repro evidence used for diagnosis.

What good looks like: the repro's own command or step re-run with a stated, checkable outcome (a value, output, exit code, rendered state, timing bound, or a named error gone), or a named regression test for the reported case. A suite run is a fine addition, never a substitute, and a feeling is not an outcome.

## Honesty

Where it lives: eval bundle, certainty wording (confirmed, verified, definitely, root cause, no risk) and risk, unknown, or open-question lines in the Candidate plan and comment, read against the Repro evidence and Thread highlights. Live mode, the same sentences in plan.md and the draft comment, plus any deviation note plan.md records after a build, read against the repro evidence and the live thread (which shows whether anything a draft calls posted or "above" exists).

What good looks like: certainty only where the evidence in hand supports it; anything unverified (an estimate, a cost, which of two sites) is named as such. A stated risk, open question, or honest deviation note is a strength. A deviation that exists only in the diff, not in plan.md, is not recorded.

## Comms

Where it lives: eval bundle, the Candidate plan comment and plan, read against the Thread highlights (role-holder direction; any patch, test build, branch, or PR in flight) and the Repo facts block (contribution policy, AI-use wording, templates). Live mode, the draft comment and plan.md, read against the live thread and its linked PRs, and against the repo's CONTRIBUTING file, issue and PR templates, and any AI policy file, read via gh or the web. In live mode, scope.md's house rules govern how the thread is read.

What good looks like: the comment engages any role-holder direction or in-flight work (adopting it, offering tests or review, rebasing onto it, or explaining a different path all count), proposes nothing a stated policy forbids, and carries a disclosure sentence when the policy requires disclosure for issue comments or for AI use in any form. A policy that asks only for human authorship, understanding, or own-words comments, or that puts disclosure on the pull request, asks nothing of the comment stage.
