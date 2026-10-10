# Scan output: saved read-outs, one section per scan

## Scan 1 (2026-10-09)

Run: headless `claude -p --model sonnet "issue-scout: run a scan"`, $0.51, 74 s, 14 permission denials. Stopped before ranking, so this read-out has no JSON block. Raw run: `unit5-drafts/runs/S1.jsonl`.

The scan is stopped before ranking, so there are no candidates yet. The three searches ran and their results are saved, but I couldn't run the ranking step.

**What worked:** Q1, Q2 and Q3 each ran with `--limit 150` and wrote their JSON to the scratchpad as `q1.json`, `q2.json` and `q3.json`. I haven't counted the results yet.

**What stopped it:** the combine, filter and sort step is a long `jq` program, and the permission layer needs approval for it. This session is non-interactive, so I can't get that approval. I tried it inline, from a file and through a shell script, and each one hit the same block. I didn't try to get around it. I also haven't done the grading step, because it needs the top 5.

**To finish, either:**
1. Approve the `jq` filter (or allowlist `jq` and `bash <scratchpad>/run.sh`) and re-run `/issue-scout`. I'll reuse the saved results and go straight to ranking and grading. Q1–Q3 are search results from today, 2026-10-09.
2. Run the executable block from `search-strategy.md` yourself with `SCAN_DATE=2026-10-09` and paste the top 5. I'll grade them from there.

The MCP servers GitHub, Gmail, Google Calendar and Google Drive also need authorizing, through `/mcp` or your claude.ai connector settings. This scan doesn't use them, since it goes through the `gh` CLI.

## Scan 2 (2026-10-09)

Run: headless `claude -p --model sonnet "issue-scout: run a scan"` with the patched executable block, $1.17, 16 permission denials (5 grader sub-agents blocked; the scout graded the five itself). Raw run: `unit5-drafts/runs/S2.jsonl`.

**Scan 2 read-out (2026-10-09).** Funnel: 449 combined, 103 after F1, 76 after the repo dedupe, top 5 graded.

All five Sonnet graders were denied `gh` and returned blocked, so I graded the five myself. The evidence is `gh issue view`, repo metadata, the last 5 commits, linked PRs from the timeline, and keyword greps of CONTRIBUTING. I did not read CONTRIBUTING in full, so policy-permits rests on those greps. For `response-latency` the first-response sample I pulled was too thin, so I graded it unclear for all five. That row is preferred-only and never changes a verdict.

**Accepted, in fit order**
1. **KRLabsOrg/LettuceDetect#118**, a Vela hallucination-detector backend (surfaced by Q2, 0 comments, newest-first tie-break).
   - **Why it fits:** Python RAG hallucination evaluation, which is your core profile. It has concrete acceptance criteria and a runnable "Start here" snippet. A collaborator opened it, and the last commit was today by a person.
   - **Unclaimed:** the linked PR #120 is closed and #119 is an open issue, not a PR.
   - **Tension:** it is a big task (backend, evaluation note and docs), and it was filed an hour before the scan. Check whether the maintainer plans to build it before you claim it.
2. **github-community-gitam/mini-jarvis-event#61**, offline FastAPI tests with a stubbed LLM (Q3, 0 comments).
   - **Why it fits:** FastAPI, pytest and mocks are exactly your test-infrastructure profile.
   - **Tension:** the repo has 0 stars, one committer, and a CSV of 60 pre-written issues. It looks like an event backlog, which the profile calls someone's to-do list. It has no CONTRIBUTING, which passes by silence.
3. **existence-master/Sentient#174**, show sources under answers (Q2, 0 comments).
   - **Why it fits:** it is sized S, touches the agent loop, and the repo welcomes AI assistants. About half the work is Electron/TypeScript frontend.
   - **Tension:** the same person filed the issue and made every recent commit, so it is also a roadmap item.
4. **timothywarner-org/agent-security-quorum#5**, an enterprise false-positive fixture (Q2, 0 comments).
   - **Why it fits:** it is evaluation work with a clear checklist.
   - **Tension:** it is Markdown plus JSON plus shell checks, not Python, and the repo has 2 stars. The issue author owns the repo and made every recent commit, but GitHub reports them as CONTRIBUTOR, so the F1 owner filter missed it.

**Rejected**
- **google/adk-python#7485** (Q3) fails `unclaimed`: PR #7467 is open and linked ("Fix: #7467"), filed by the issue author. Every other check passed, and the repo has an AGENTS.md.

Verdicts for items 1 to 4 come from the rubric. The fit order is the profile's call and never changes a verdict.

```json
[
  {"item": "https://github.com/KRLabsOrg/LettuceDetect/issues/118", "checks": [
    {"name": "repo-alive", "grade": "pass", "evidence": "archived false, pushed 2026-10-09"},
    {"name": "maintainers-active", "grade": "pass", "evidence": "latest commits 2026-10-09 by c71qu3 and adaamko; collaborator opened the issue"},
    {"name": "policy-permits", "grade": "pass", "evidence": "CONTRIBUTING keyword grep found no AI ban"},
    {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; linked PR #120 closed; #119 is an open issue, not a PR"},
    {"name": "scope-bounded", "grade": "pass", "evidence": "one backend with acceptance criteria; follow-ups explicitly excluded"},
    {"name": "project-wants-it", "grade": "pass", "evidence": "opened by COLLABORATOR with labels"},
    {"name": "response-latency", "grade": "unclear", "evidence": "sample too thin; preferred, no effect on verdict"}
  ], "verdict": "accept"},
  {"item": "https://github.com/github-community-gitam/mini-jarvis-event/issues/61", "checks": [
    {"name": "repo-alive", "grade": "pass", "evidence": "archived false, pushed 2026-10-09"},
    {"name": "maintainers-active", "grade": "pass", "evidence": "commits 2026-10-09 by ramkolipakula, a person"},
    {"name": "policy-permits", "grade": "pass", "evidence": "no CONTRIBUTING; silence passes"},
    {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no linked PRs; 0 comments"},
    {"name": "scope-bounded", "grade": "pass", "evidence": "one test suite with acceptance criteria"},
    {"name": "project-wants-it", "grade": "pass", "evidence": "carries testing, advanced and backend labels"},
    {"name": "response-latency", "grade": "unclear", "evidence": "sample too thin; preferred, no effect on verdict"}
  ], "verdict": "accept"},
  {"item": "https://github.com/existence-master/Sentient/issues/174", "checks": [
    {"name": "repo-alive", "grade": "pass", "evidence": "archived false, pushed 2026-10-09"},
    {"name": "maintainers-active", "grade": "pass", "evidence": "commits 2026-10-09 by itsskofficial, a person"},
    {"name": "policy-permits", "grade": "pass", "evidence": "CONTRIBUTING: 'Using AI coding assistants. Welcome.'"},
    {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no linked PRs; 0 comments"},
    {"name": "scope-bounded", "grade": "pass", "evidence": "one feature, 'Size: S', named files"},
    {"name": "project-wants-it", "grade": "pass", "evidence": "carries enhancement, help wanted and area labels"},
    {"name": "response-latency", "grade": "unclear", "evidence": "sample too thin; preferred, no effect on verdict"}
  ], "verdict": "accept"},
  {"item": "https://github.com/timothywarner-org/agent-security-quorum/issues/5", "checks": [
    {"name": "repo-alive", "grade": "pass", "evidence": "archived false, pushed 2026-10-09"},
    {"name": "maintainers-active", "grade": "pass", "evidence": "commits 2026-10-09 by timothywarner, a person"},
    {"name": "policy-permits", "grade": "pass", "evidence": "CONTRIBUTING keyword grep found no AI ban"},
    {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; only linked PR #4 is closed"},
    {"name": "scope-bounded", "grade": "pass", "evidence": "one fixture with acceptance criteria"},
    {"name": "project-wants-it", "grade": "pass", "evidence": "carries enhancement, good first issue and help wanted labels"},
    {"name": "response-latency", "grade": "unclear", "evidence": "sample too thin; preferred, no effect on verdict"}
  ], "verdict": "accept"},
  {"item": "https://github.com/google/adk-python/issues/7485", "checks": [
    {"name": "repo-alive", "grade": "pass", "evidence": "archived false, pushed 2026-10-09"},
    {"name": "maintainers-active", "grade": "pass", "evidence": "five commits on 2026-10-09 by DeanChensj and jazhang00"},
    {"name": "policy-permits", "grade": "pass", "evidence": "CONTRIBUTING has an AI-Assisted Development section and AGENTS.md exists"},
    {"name": "unclaimed", "grade": "fail", "evidence": "linked PR google/adk-python#7467 is open, filed by the issue author; body says 'Fix: #7467'"},
    {"name": "scope-bounded", "grade": "pass", "evidence": "a defect report with a mocked reproduction"},
    {"name": "project-wants-it", "grade": "pass", "evidence": "a defect report"},
    {"name": "response-latency", "grade": "unclear", "evidence": "sample too thin; preferred, no effect on verdict"}
  ], "verdict": "reject"}
]
```

## Scan 3 (2026-10-09)

Run: headless `claude -p --model sonnet "issue-scout: run a scan"` on the patched rubric and strategy, $0.83, 3 harness denials, grading inline. Raw run: `unit5-drafts/runs/S3.jsonl`.

## issue-scout scan, 2026-10-09

**Funnel:** 449 combined results → 81 after F1 → 62 after repo dedupe → top 5 graded.
**Result:** 3 accepted, 2 rejected. Both rejections were on `unclaimed`.

### Accepted, in fit order

1. **KRLabsOrg/LettuceDetect#118**, "Add a decision-model detector backend (Vela 2.0 first)".
   - **Found by:** Q2. **Ranking signals:** 0 comments, created 2026-10-09, COLLABORATOR opener.
   - **Why it fits:** It is squarely in the AI layer, a hallucination detector backend with its own evaluation reproduction.
   - **Project health:** Maintainer `adaamko` replied to outside issues in 2, 10 and 15 days, so `response-latency` passes. Outside PRs have been merged, including tests and a CLI.
   - **Reproduction path:** The issue has a "Start here" snippet and acceptance criteria. The test suite is network-free.
   - **Linked items:** #120 is a closed, merged roadmap PR. #119 is a separate open issue with no assignee.
   - **Tension:** The scope is large: a new backend, an F1 reproduction within ±0.01 on 10,698 examples, and docs. The rubric passes this as one enumerated plan inside one repo. It is the hardest of the three to finish in four weeks.

2. **Traigent/Traigent#2516**, "bug(mock): TRAIGENT_MOCK_LLM=true run has no mock label, shows non-zero cost".
   - **Found by:** Q3. **Ranking signals:** 0 comments, created 2026-10-07.
   - **Why it fits:** It is a bug in the mock-LLM test seam with a runnable repro and clear expected behaviour.
   - **Latency:** `unclear`. Every sampled issue was opened by `IsraelTraigent`, and the maintainer never commented.
   - **Tension, worth reading before committing:** CONTRIBUTING says PRs targeting `main` or `develop` must be authored by `nimrodbusany`, with contributor branches handed off to them. A CLA is also required. The repo has 2 stars, and `nimrodbusany` made 49 of the last 50 commits. The rubric passes all of this, but it is a real friction cost.

3. **existence-master/Sentient#174**, "Show sources under answers".
   - **Found by:** Q2. **Ranking signals:** 0 comments, created 2026-10-09.
   - **Why it ranks last:** It spans the agent loop and a React component, which is partly frontend work. Latency is `unclear` because every recent issue was filed by the sole committer.
   - **Tension:** The opener `itsskofficial` made the visible commits, and the issue is a roadmap item ("Roadmap ID: UX-3"). That is the "owner's to-do list" case your profile wants to avoid. F1 missed it because the org name `existence-master` does not start with the login.
   - **Policy:** AI assistants are explicitly welcome. A CLA bot applies.

### Rejected

- **google/adk-python#7485** (found by Q3): fails `unclaimed`. The opener's own open PR #7467 says "Closes: #7485", and the issue says "Fix: #7467". The linked PR is open.
- **useagenthq/threads#5** (found by Q2): fails `unclaimed`. Open PR #7 says "Resolves #5" and "/claim #5". This is also a solo repo with bounties, and its issue text says AI help is fine.

### Ranking friction for `scan-notes.md`

- **F1 gap:** An owner-equivalent opener is missed when the org name is not a prefix of the login. Sentient and threads were both single-author backlogs. A cheap fix is to drop issues where the same author opened most of the repo's recent results, using only fields already fetched.
- **F2 gap:** Sorting by fewest comments ranks fresh issues first, before a PR can exist. Both rejections were PRs filed against issues less than a day or two old, and the `cross-referenced` open PR only shows up at grading. This is a rubric catch, not a ranking bug.

### Not fully checked

- **adk and threads policy:** I did not read these in full, because both were rejected on `unclaimed` and the rubric lets that decide. For adk I only searched CONTRIBUTING for AI mentions.
- **Rejected candidates' latency:** adk is a pass (a collaborator replied the same day on #7462). Threads is `unclear`.

```json
[
  {
    "item": "https://github.com/KRLabsOrg/LettuceDetect/issues/118",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived=false; last push 2026-10-09, gap 0 days"},
      {"name": "maintainers-active", "grade": "pass", "evidence": "adaamko (human) committed 2026-10-09; COLLABORATOR opened the issue"},
      {"name": "policy-permits", "grade": "pass", "evidence": "read in full: no AI statement (CONTRIBUTING.md, PULL_REQUEST_TEMPLATE.md; no AGENTS.md/CLAUDE.md in repo)"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; cross-ref PR #120 is closed/merged roadmap-only; #119 is a separate open issue"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "enumerated acceptance criteria inside one repo; none of the five disqualifiers apply"},
      {"name": "project-wants-it", "grade": "pass", "evidence": "opened by adaamko (COLLABORATOR), labels enhancement + help wanted"},
      {"name": "response-latency", "grade": "pass", "evidence": "first maintainer reply 2 days (#117), 10 days (#115), 15 days (#98)"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/Traigent/Traigent/issues/2516",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived=false; last push 2026-10-09, gap 0 days"},
      {"name": "maintainers-active", "grade": "pass", "evidence": "nimrodbusany (human) committed 2026-10-06; 49 of last 50 commits"},
      {"name": "policy-permits", "grade": "pass", "evidence": "read in full: CONTRIBUTING.md, docs/contributing/CONTRIBUTING.md, PR template; no AI statement (CLA + nimrodbusany-only protected-target rule are process conditions)"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no linked PRs in timeline; 0 comments"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "bug report: expected vs actual stated; related issues are references, not a list to pick from"},
      {"name": "project-wants-it", "grade": "pass", "evidence": "defect report carrying label 'bug'"},
      {"name": "response-latency", "grade": "unclear", "evidence": "all sampled issues opened by IsraelTraigent got no maintainer comment; fewer than 3 entries qualify"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/existence-master/Sentient/issues/174",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived=false; last push 2026-10-09, gap 0 days"},
      {"name": "maintainers-active", "grade": "pass", "evidence": "itsskofficial (human) committed 2026-10-09"},
      {"name": "policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md: 'Using AI coding assistants: Welcome.'; AGENTS.md and CLAUDE.md present"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no linked PRs; 0 comments"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "'Size: S', 'Done when' lists two criteria, files named"},
      {"name": "project-wants-it", "grade": "pass", "evidence": "carries project labels (enhancement, help wanted, area/priority/size)"},
      {"name": "response-latency", "grade": "unclear", "evidence": "every recent non-PR issue was opened by the sole committer itsskofficial; no outside-opener sample exists"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/google/adk-python/issues/7485",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived=false; last push 2026-10-09, gap 0 days"},
      {"name": "maintainers-active", "grade": "pass", "evidence": "DeanChensj (human) committed 2026-10-09"},
      {"name": "policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md 'AI-Assisted Development' section welcomes agents; AGENTS.md present (not read in full; rejected on unclaimed)"},
      {"name": "unclaimed", "grade": "fail", "evidence": "open PR #7467 by the opener: 'Closes: #7485'; issue body says 'Fix: #7467'"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "defect report with mocked repro script and expected/observed output"},
      {"name": "project-wants-it", "grade": "pass", "evidence": "defect report, opener CONTRIBUTOR"},
      {"name": "response-latency", "grade": "pass", "evidence": "collaborator surajksharma07 replied same day on #7462 (0 days)"}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/useagenthq/threads/issues/5",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived=false; last push 2026-09-29, gap 10 days"},
      {"name": "maintainers-active", "grade": "pass", "evidence": "abhishekgahlot2 (human) committed 2026-09-29"},
      {"name": "policy-permits", "grade": "pass", "evidence": "issue text: 'AI help is fine. Blind AI output is not.'; AGENTS.md present (CONTRIBUTING not read in full; rejected on unclaimed)"},
      {"name": "unclaimed", "grade": "fail", "evidence": "open PR #7 by boumarate: 'Resolves #5' and '/claim #5'"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "'Done when' lists three scorers in two languages inside one repo"},
      {"name": "project-wants-it", "grade": "pass", "evidence": "labels enhancement, help wanted, hacktoberfest"},
      {"name": "response-latency", "grade": "unclear", "evidence": "all recent issues opened by the sole maintainer; no outside-opener sample"}
    ],
    "verdict": "reject"
  }
]
```

