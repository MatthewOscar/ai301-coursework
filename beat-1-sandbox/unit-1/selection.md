# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/22

**Verdict output**

```
## Grading: Issue #22 — "Add a mock GitHub API server for integration tests"

Repo: `codepath/pathreview-ai301-fa26-s1` (in scope; Path Review house rule applies — claim comments from classmates don't block).

| Check | Grade | Evidence |
|---|---|---|
| repo-alive | pass | `archived: false`; last push 2026-09-10T21:31Z, ~4 days before capture (2026-09-14) |
| maintainers-active | pass | Last 5 default-branch commits all authored by a person (Andrew Burke / `Aburke225`), most recent 2026-08-24, ~21 days before capture — well under the 180-day window |
| policy-permits | pass | `docs/CONTRIBUTING.md` exists (linked from README) and contains no AI-use restriction of any kind; silence passes |
| unclaimed | pass | `assignees: []`; no cross-referenced/linked PRs in the timeline; 0 comments on the issue |
| scope-bounded | pass | Single bounded task: build a mock GitHub API server (`pytest-httpserver` or similar) to unskip `tests/integration/test_github_tool.py`; relevant files and a 5–7h estimate given; no TBD blocking implementation |
| project-wants-it | pass | Opener `Aburke225` has `author_association: COLLABORATOR` (course staff), so the fail condition (anonymous/bot opener + no labels + no maintainer comment) never triggers |
| response-latency (preferred) | unclear | Sampled 15 recently-updated issues — all have 0 comments ("no maintainer comment in thread"); this is a freshly-seeded course batch (all created within seconds of each other on 2026-09-10), not evidence of neglect |

**Verdict: accept.** All six required checks pass cleanly.

Fit note (from your profile): this sits in the agent/test-infrastructure layer you want more reps in — it's not retrieval/reranking/eval directly, but it's exactly the "test scaffolding that makes any of it verifiable" work you called out, and it's substantial (5–7h, real design: fixture design + mock server wiring), not a typo fix. Reproduction path is clean — the failing/skipped tests are named (`tests/integration/test_github_tool.py`), the fixture directory already exists as a seam, and there's no maintainer sign-off needed before starting.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/22",
  "checks": [
    {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; last push 2026-09-10, 4 days before capture (2026-09-14)"},
    {"name": "maintainers-active", "grade": "pass", "evidence": "last 5 commits all by human author Aburke225, most recent 2026-08-24 (21 days before capture)"},
    {"name": "policy-permits", "grade": "pass", "evidence": "docs/CONTRIBUTING.md exists, no AI-use restriction stated; silence passes"},
    {"name": "unclaimed", "grade": "pass", "evidence": "assignees: []; no linked/cross-referenced PRs; 0 comments"},
    {"name": "scope-bounded", "grade": "pass", "evidence": "single bounded ask: add a mock GitHub API server to unskip named tests, files and 5-7h estimate given, no blocking TBD"},
    {"name": "project-wants-it", "grade": "pass", "evidence": "opener Aburke225 has author_association COLLABORATOR"},
    {"name": "response-latency", "grade": "unclear", "evidence": "sample of 15 recent issues all show 0 comments; freshly-seeded batch, not a neglect signal"}
  ],
  "verdict": "accept"
}
```
```

---

## Eval iterations

**Run history**

1. **18/20 — PASS.** First full run of the finished rubric.
   `categories: claimed 4/4  clear-accept 7/8  dead-repo 3/3  policy 1/1  scope 3/4`
   Two disagreements, both on `scope-bounded`, pulling in opposite directions: issue-19
   (gold accept, my rubric rejected) and issue-15 (gold reject, my rubric accepted).
2. **5/5 on a partial `--only` probe** of issue-05, issue-08, issue-10, issue-19, issue-20,
   after narrowing `scope-bounded` clause (c). I ran this before spending another full run
   because I needed to know whether the fix flipped issue-19 without breaking the four other
   scope rejects. issue-19 flipped to accept; the other four held. Partial runs print no bar
   verdict and cannot write `eval-run.txt`, which is why this is not the committed run.
3. **19/20 — PASS.** Confirming full run, the one committed in `eval-run.txt`.
   `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 3/4`
   `clear-accept` went from 7/8 to 8/8. issue-15 remains the single disagreement.

Before any of these I ran the rubric offline against all 24 bundles to check the accept/reject
split and the format gates, so the first paid run was not a guess.

**Issue analysis**

**issue-15** (zulip/zulip#19589, "Separate `command` and `text` field for slack-compatible
outgoing webhook"). My rubric grades it **accept**; the gold label is **reject**, in the
`scope` category. It is the one item my final rubric still disagrees with.

My rubric accepted it because every required check passes on the visible evidence. The repo
is alive and maintainers are active; the policy is a condition, not a ban; `assignees: none`
with all linked PRs closed, and the claim-and-unassign cycles in the thread are dated 2023
and 2024, far outside my 180-day window, so `unclaimed` passes. `scope-bounded` passes
because the body reads as one bounded behavioural change — separate the bot mention into a
`command` field — and the one speculative sentence in it, "Perhaps we can also transform the
mention (`**@mybot**`) to a slash command (`/mybot`)", is caught by my own carve-out treating
items marked "perhaps" as optional extras rather than undecided inputs.

What my rubric could not see is that this is a five-year-old design conversation. The thread
shows 97 comments of which the bundle displays 40, and inside that window a MEMBER
(timabbott) questions the premise itself — that Slack's documented API "does not suggest the
presence of a `command` field". The design was never settled. My clause (e) fires on an
unsettled design, but only when the issue *states* the openness — a TBD, a "is this by
design?", a "confirm before implementing". It does not fire when the doubt lives in a
maintainer's question 40 comments deep.

I chose not to chase this one. A rule tight enough to catch it would have to treat a
maintainer's question as unsettling the design even where the reporter answered it, and I
could only have justified that rule by knowing the gold label first. The assignment says four
of the twenty are genuinely arguable scope calls; I would rather submit a rubric that misses
one of them for a stated reason than one tuned backwards from the answer key.

**Check rationale**

From the `scope-bounded` row, clause (c), as it currently reads:

> (c) An issue whose PRIMARY deliverable is architectural or performance rework of internals — the re-architecture itself is the ask (rewrite this subsystem, parallelise this engine, change this data structure across the codebase), with no reported malfunction driving it. Apply a single test: if you removed the internals work, would anything remain for the issue to deliver? If nothing remains, this disqualifier fires. A defect report does NOT become this merely because it names candidate causes, proposes optimisations, or lists suggested improvements alongside the fix, however technical those suggestions are and however many of them there are; nor does speculation about where a cause might live.

Its first form was blunter: it failed any issue that named "concurrency, parallelism,
algorithmic-complexity rework, or core engine, matcher or parser rework", or that "lists
several independent root causes still to be fixed". That wording cost me issue-19
(zxcalc/zxlive#517, "Selecting large subgraphs in proof mode freezes the UI"), a genuine
gold-accept. Its body reports a real malfunction — the UI freezes — and then offers the
reporter's own theories: two candidate causes, and three "Additional suggestions" involving
multiprocessing and threading. The old clause read those theories as the work.

Checking `baseline.json` showed clause (c) was load-bearing for exactly one issue in the set,
and it graded that issue wrong. Every other scope reject rode a different clause: (b) for
issue-05, (a) for issue-10, (e) for issue-08 and issue-20. So I rewrote (c) around the
distinction that actually matters — whether the rework *is* the deliverable, or is one
person's guess at how to reach a deliverable that stands on its own. The "remove the
internals work and see if anything remains" test is there so the rule can be applied by
someone who has never seen these bundles.

**Trade-offs**

The clause now gives up the ability to reject a hard internals job that is *dressed* as a
bug report. An issue reading "rendering is wrong on large documents" whose real fix is a
rewrite of the layout engine now passes `scope-bounded`, because a reported malfunction
drives it and the removal test leaves the malfunction behind. That is a deliberate trade: in
this corpus, mistaking a bug report for internals work cost me a clear-accept, while the
reverse error cost nothing, because the genuinely unscoped issues are all caught by clauses
(a), (b) and (e) instead.

I verified this rather than assuming it. Before committing the change I re-ran the four scope
rejects that clause (c) was *not* responsible for as a canary —
`--only issue-05,issue-08,issue-10,issue-19,issue-20`, about $1 — and all four held their
reject while issue-19 flipped to accept. The confirming full run then showed the same thing
at full scale: `clear-accept` improved from 7/8 to 8/8 and no other category moved. Nothing
else changed, and the canary is how I know.

---

## Selection rationale

**Selection rationale**

**1. Fit to my interests and the time available.** I want to get better at the AI layer of a
system and I want work substantial enough to describe as engineering. #22 is tier-2 and
estimated at 5–7 hours, which is real work rather than a typo fix, and it sits in the test
scaffolding that makes the agent's external tooling verifiable. It is not retrieval or
evaluation code itself, which is why my own skill ranked it third of the three candidates
behind #59 and #13. I picked it anyway, and the reason is the next answer.

**2. What the verdict identified correctly, and what I weighed that the rubric could not.**
The verdict was right about everything it could see: the repo is alive and pushed four days
before I graded it, the last five commits are human-authored, `docs/CONTRIBUTING.md` states
no AI restriction, the issue has no assignee, no linked PR and no comments, and the ask is
one bounded task with its files named and no blocking TBD. All six required checks passed
cleanly.

What it could not weigh is the state of the code behind the issue text. I read the repository
before choosing. #22's dependencies are real: `GitHubTool.__init__` sets
`self.base_url = "https://api.github.com"` as a plain instance attribute, which is exactly
the seam a mock server needs, and `pytest-httpserver>=1.0.8` is already declared in the dev
dependencies. By contrast my skill graded #13 `scope-bounded: pass` partly on the issue's
claim that it would use "the mock LLM provider" — and no such provider exists. The only mock
in the repo is `MockEmbeddingProvider`, for embeddings, and `ReviewGenerator.__init__` hard
constructs `openai.OpenAI(...)` with no injectable client. My rubric grades issue text, so it
believed a stated dependency that is not there. That is the limit of the tool, and checking
it is the part that stays mine.

I also noted that both #22 and #13 describe files that do not yet exist — `tests/integration/`
contains only an empty `__init__.py` and `tests/fixtures/` is absent — so #22's premise that
tests are "currently skipped" is loose. That does not change the work; it just means I am
creating the first agent integration test rather than unskipping one.

**3. Anticipated difficulty in claiming it.** Low. The issue is unassigned, has zero comments,
no linked PR, and was seeded by course staff (`Aburke225`, COLLABORATOR) in a batch on
2026-09-10. The Path Review house rule says classmates' claim comments do not block an issue
anyway, so even if someone comments before I do, the issue stays available and credit
attaches to the pull request I open. The real difficulty is not claiming but scoping the
fixture set: deciding how many GitHub API responses to mock and how faithful they need to be
is a judgement I will have to make before writing the first test.

---

Related paths: `eval-run.txt` in this directory; my skill's files in `tools/issue-select/`.
