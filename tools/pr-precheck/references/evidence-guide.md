# Evidence guide: where evidence lives in a PR package

Eval mode reads only the bundle. Live mode reads the working copy and the real repo, read-only. In live mode, a failed retrieval is 'unavailable', never proof of absence.

Terms used below:

- The plan is the accepted plan the PR claims to implement.
- A deviation note is a line in the plan that records a change from it or a deferred slice.
- Repo facts are the repo's stated template asks and contribution policy.

## Plan fidelity (harness category: silent-drift)

Where it lives: in an eval bundle, read the Plan context block (scope, Files list, not-in-scope line, deviation or limit notes) against every file header and hunk in the Candidate PR's Diff. Then read the Description's fidelity claims against both. In live mode, read plan.md (scope, files, deviation notes) against `git diff main...HEAD` (default branch substituted) and `git log main..HEAD`. Then read the description in pr_draft.md (everything after the first line).

What good looks like: every changed file falls inside the plan's scope or a deviation note. Every behavior the hunks add (an option, flag, setting, API, rename, rewrite, version bump, or second fix) is one the plan names. Tests, changelog or release-note entries, and translation strings that serve the fix belong to it, even when the Files list omits them. Every deliverable the plan names (code, docs page, changelog, test) appears in the diff, or the plan or description says it was dropped or deferred.

Only a deviation note in the plan can re-tie a mismatch. Silent drift runs both ways: more than the plan (an unplanned file or behavior), or less with no note (a named deliverable missing). Check description claims against the hunks rather than believing them: 'exactly the plan', 'nothing beyond', 'no other changes', 'docs updated', 'changelog added'. A claim the diff contradicts is drift, not a comms problem. Debris belongs to diff quality, not here.

## Test evidence (harness category: not-tested)

Where it lives: in an eval bundle, read the Candidate PR's Test evidence section, plus any outcome lines in the Description. Read them against the Plan context's repro evidence and test plan and the Issue's repro steps. The Repo facts block and the plan name the repo's own checks. In live mode, read test_evidence.md and pr_draft.md against plan.md's test plan, the repro in the issue thread, and the checks named in CONTRIBUTING and .github/PULL_REQUEST_TEMPLATE.md.

What good looks like:

- The reported behavior is run on the path the diff changes. The evidence shows the repro step or command and a concrete after: captured output, a value, an exit code, a quoted message, or a rendered state. The after differs from the before the way the plan expects. The before may come from the plan's repro evidence.
- Each test-plan item (each named repro, failure mode, control, platform, or docs or render check) is shown, stated with its outcome ('control unchanged'), covered by an added test whose run is shown, or deferred with a reason.
- Each stated repo check has a visible outcome ('612 passed', 'fmt and clippy clean', 'code formatted').

Not proof: 'tested locally, works now', a day of use, a suite count alone, a control without the reported case, an unrelated scenario, or an empty section. An item that is never mentioned is absent, even if it shares code with a shown item. An honest note of what was not run, and why, bounds a side case. An untested reported behavior still fails.

## Diff quality (harness category: unreviewable)

Where it lives: in an eval bundle, the Candidate PR's Diff (every hunk, test and changelog files included) and the Commits list. In live mode, `git diff main...HEAD` and `git log main..HEAD`.

What good looks like: the fix is visible and nothing rides along with it.

Debris tells:

- debug prints or logging, including commented-out ones
- commented-out code or kept experiments
- functions nothing calls, or allow-dead-code shims
- stray TODO notes
- import reordering
- reformatted or re-indented lines
- identical lines removed and re-added in statements the change does not edit
- unrelated drive-by edits

Not debris:

- a comment explaining the fix, or an existing comment the change made stale and updated
- the unchanged lines of a statement being edited, shown as removed and re-added
- production logging or warnings the plan calls for
- commit messages (their wording and count never decide)

## Standards and comms (harness category: standards-wall)

Where it lives: in an eval bundle, the Repo facts block holds the PR-template sections and asks, the contribution asks (such as a changelog entry, an issue link, or tests), and the stated AI policy. Read it against the Description, and against the Diff for changelog, whatsnew, or test files. In live mode, read .github/PULL_REQUEST_TEMPLATE.md (or the repo's equivalent), CONTRIBUTING, any AI policy file, and the issue thread against pr_draft.md and the diff.

What good looks like:

- Every section or ask the repo states as required has real content, under any heading.
- A required issue link or closing reference is present.
- A required changelog or release-note entry exists in the diff.
- Checklist items are met, or marked not-applicable with a reason.

Two kinds of ask create no duty in the PR text: an ask conditioned on something absent, and a process step the PR text cannot show (a CLA, a vouch flow).

AI disclosure: where the policy text requires AI-use disclosure on pull requests, or for all AI use, the description carries a sentence saying AI assisted the work. That sentence includes any particulars the policy names (the tool, the extent). A policy that is silent on AI, or that asks only for human review, understanding, or own-words writing, creates no disclosure duty.

Fails: an empty required section, a skipped required entry, or a missing owed disclosure. (Whether claims match the diff is plan fidelity, above.)
