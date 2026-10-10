# Scan notes: where the wild surprised my tooling

One dated line per moment. Each line names the check or filter, quotes what confused it, and says
where the fix routes (rubric, strategy, or none).

## Friction

- 2026-10-09 (strategy, before scan 1) Queries: a whole OR expression in one pair of quotes,
  `"rag OR retrieval OR reranker"`, came back with 0 results, because gh searched it as a single
  phrase. Fixed in search-strategy.md: OR terms are now separate unquoted arguments.
- 2026-10-09 (strategy, before scan 1) Ordering: a draft that sorted `updatedAt` newest first put
  llama_index#23429 and dnb-ai#433 in its top 5 only because a claim comment had just bumped
  them ("the author has already opened a fix PR"). Fixed: F2 orders by `commentsCount` then
  `createdAt`, never `updatedAt`.
- 2026-10-09 (strategy, before scan 1) Queries: the drafts' top fives included my own sandbox,
  `codepath/pathreview-ai301-fa26-s1#13` and its s3 twin, both claimed by classmates. Fixed: F1
  drops `codepath/*`.
- 2026-10-09 (strategy, scan 1) Ranking filters F1/F2, executable block: the scout's permission
  layer refused every form of it, 14 denials in all: "Contains brace with quote character
  (expansion obfuscation)" for the `{ gh ...; } | jq` group, "jq command contains dangerous flags
  that could execute code or read arbitrary files" for `jq -f`, and "Shell expansion syntax in
  paths requires manual approval" for `--arg scan`. Scan 1 stopped before ranking and graded
  nothing ($0.51). Patched search-strategy.md: two steps (each query saved to the scratchpad,
  then one single-line `jq -s` using `now`). Before trusting it, two Haiku permission probes
  checked which shapes pass (0 denials on the final shape), and the new command was re-run on
  scan 1's saved query files: same funnel, 449 → 103 → 76 → 5.
- 2026-10-09 (none, scan 1) Held up: the scout would not rank by hand once jq was blocked ("I
  didn't try to get around it"). That is SKILL.md's "no model judgment calls" ranking discipline
  doing its job, so no patch.
- 2026-10-09 (harness, scan 2) Grading stage: the scout handed the five candidates to grader
  sub-agents, and all five were denied `gh` ("This command requires approval"), so the scout
  graded them inline. That is my headless runner, not the tool: from scan 3 the runner disallows
  sub-agents. Its `$S` redirect denial was the same kind of thing, and the strategy already says
  to use a literal path.
- 2026-10-09 (strategy, scan 2) F1, the `OWNER` drop: timothywarner-org/agent-security-quorum#5
  was filed by `timothywarner`, who wrote all 25 commits in the repo, but it reads `CONTRIBUTOR`
  because the org is named after him. It reached grading and was accepted. Patched (P3).
- 2026-10-09 (strategy, scan 2) F1 has no event filter: github-community-gitam/mini-jarvis-event#61
  passed all six required rows. The repo was created that morning (08:11Z), and its only committer
  seeded 60 issues in 71 minutes ("Seed defects for issues"). It has 0 stars and 0 forks, and the
  opener reads `COLLABORATOR`, so no association filter catches it. Patched (P4).
- 2026-10-09 (none, scan 2) maintainers-active and project-wants-it on those two repos: there is no
  misread. Each owner committed on scan day and labelled the issue, so a human really was there to
  review. What sets these repos apart is adoption and fit, which my rubric keeps out of verdicts on
  purpose ("star count ... may never produce a fail"). The fix routes to the search, not to a new
  rubric disqualifier. A "last 5 commits all by the opener" rule would also have misfired on
  Sentient, which has 4 human authors and 2 merged outside PRs.
- 2026-10-09 (rubric, scan 2) response-latency: all five got "unclear" because "the first-response
  sample I pulled was too thin". The evidence was there. LettuceDetect's outside issues got first
  replies in 2, 10 and 15 days, and adk-python#7438 in 0.7 days. On Sentient the maintainer reads
  `CONTRIBUTOR` (private org membership), so a badge-only reading cannot tell him apart. The row
  named a bundle line that the wild does not carry. Patched (P1).
- 2026-10-09 (rubric, scan 2) policy-permits: all five were graded from "keyword greps of
  CONTRIBUTING". Every pass held on a full read, but LettuceDetect's deciding sentence ("you
  confirm that you wrote the contribution") has no AI keyword in it, and the grep missed
  AGENTS.md or CLAUDE.md twice. Patched (P2).
- 2026-10-09 (none, scan 2) unclaimed: google/adk-python#7485 was rejected correctly ("Fix: #7467"
  in its own body, and that PR is open). The patched ranking puts it back in slot 1 for scan 3. I
  tested a body filter and did not add it: the tight pattern matches 1 of 449, and the loose one
  also drops Traigent#2516 over "(closed #1419". A filter fitted to one example is taste, and the
  rubric catches this case for about $0.20.
- 2026-10-09 (none, scan 2) unclaimed evidence wording: "linked PR #120 closed" was actually merged
  and roadmap-only ("Roadmap-only change"). No grade moved.
- 2026-10-09 (none, scan 2) Tensions no row covers. LettuceDetect#118 was under 14 hours old, and
  its "±0.01 F1" reproduction runs a 0.3B model on 10,698 examples, which is a four-week fit
  question. Sentient#174 was partly shipped by the owner's PR #200 that same morning. adk-python
  asks people to "kindly ask before contributing".
- 2026-10-09 (strategy, scan 2) The scan moment is not recorded, so the auditors had to guess it.
  No patch, because no grade or rank moved.

## Patches

All four respond to scan-2 lines above. They were vetted by five live auditors (each re-graded
one candidate from scratch), a router, and an adversarial critic per patch that simulated it on
the 20 eval bundles. Result: no softening, and 0 eval verdicts that would newly disagree with gold.

- 2026-10-09 P1, rubric.md response-latency, Evidence cell. Live mode only: build the sample from
  the 5 most recently updated non-maintainer issues within 365 days of the scan date, and count
  the top committer of the last 50 commits as a maintainer. The pass condition is unchanged, and
  this row never changes a verdict. Grades it would move: LettuceDetect and adk-python go from
  unclear to pass, and Sentient goes from unclear to fail (its owner's replies took 165 to 277
  days). Fail is the right reading there: the row is "within 90 days", and the year-long gap was
  real.
- 2026-10-09 P2, rubric.md policy-permits, Evidence cell. Live mode only: read CONTRIBUTING in
  full (root, .github/, docs/), plus the contributor-docs pages it links to, AI policy files and
  the PR template. Note AGENTS.md or CLAUDE.md, and quote the deciding sentence. The critic caught
  that my first draft left out linked docs pages, which is exactly where the eval set's one policy
  reject (bookwyrm) keeps its ban. No verdict flips.
- 2026-10-09 P3, search-strategy.md F1 `OWNER` bullet: also drop when `author.login` (at least 3
  characters) starts the org name. This drops 4 of 449, including agent-security-quorum#5. It
  costs AyinkxLab#367, which is not a solo repo, and I accept that trade.
- 2026-10-09 P4, search-strategy.md F1 new bullet: drop repos with an event or course word as a
  name segment. There are 11 words, each one seen on a seeded backlog in scan 2. The critic cut
  five guessed words (workshop, bootcamp, course, tutorial, semester), because they would have
  dropped huggingface/course and pytorch/tutorials.
- references/evidence-guide.md: the latency and policy rows now match P1 and P2.
- Saved verdicts after the patches: agent-security-quorum#5 and mini-jarvis-event#61 were scan-2
  accepts. The rubric's required rows did not change, so they would still pass grading; the
  strategy now keeps them out of the top 5. That is the right reading, because the profile says
  an owner's solo repo or a seeded event backlog "is someone's to-do list". The other three scan-2
  verdicts stand.
- Live check of the patched funnel (`unit5-drafts/runs/v3/`): 449 → 81 → 62. Top 5: adk-python#7485,
  LettuceDetect#118, Sentient#174, useagenthq/threads#5, Traigent#2516.

## Scan 3 check (2026-10-09)

Scan 3 ran on the patched files: 449 → 81 → 62, 3 accept, 2 reject, $0.83. Both scan-2 rubric
frictions are fixed. response-latency is now graded from a real sample: LettuceDetect "first
maintainer reply 2 days (#117), 10 days (#115), 15 days (#98)", and adk-python "replied same day
on #7462". policy-permits now reads files in full: LettuceDetect "read in full: no AI statement
(CONTRIBUTING.md, PULL_REQUEST_TEMPLATE.md ...)". Neither backlog repo reached the top 5. That meets
the stop rule (the re-scan fixed what scan 2 logged), so I stopped at three scans (~$2.50) and
left the lines below open for next week's fresh scan.

- 2026-10-09 (none, scan 3) response-latency on Sentient#174: the audit predicted P1 would turn
  this into a fail (owner replies of 165 to 277 days), but the scout graded it unclear ("no
  outside-opener sample exists"). I checked: no non-owner issue in Sentient was created within
  365 days of the scan, so the sample is empty and unclear is the row's own answer. The patch
  held, and the audit's prediction was wrong.
- 2026-10-09 (none, scan 3) unclaimed: 2 of 5 grading slots went to issues already claimed by an
  open PR. google/adk-python#7485 was claimed by its opener's PR #7467 ("Closes: #7485"), and
  useagenthq/threads#5 by bounty PR #7 ("/claim #5"). Both issues were under two days old.
  Ordering by fewest comments, then newest, favours issues whose PR has not shown up in any
  search field yet. The rubric caught both. If next week's scan spends slots the same way, the
  ranking-stage fix to test is a minimum age (createdAt at least 3 days before the scan), checked
  free on the saved results before paying for grading.
- 2026-10-09 (strategy, scan 3) F1 owner drop, P3: existence-master/Sentient#174 and
  useagenthq/threads#5 were filed by their repos' top committers (`itsskofficial`,
  `abhishekgahlot2`), but neither org name starts with the login, so P3's prefix test let them
  through. The scout suggests dropping issues whose author opened most of that repo's results in
  the combined set. Not patched yet: Sentient is not a solo repo (676 and 319 commits from two
  humans), so that rule would cut more than the profile's "own solo repo" sentence asks for.
  Untested at grading.
- 2026-10-09 (none, scan 3) Tensions no row covers. Traigent#2516's CONTRIBUTING requires that
  PRs to `main` or `develop` "must be authored by nimrodbusany" (contributor branches are handed
  off), plus a CLA, and nimrodbusany made 49 of the last 50 commits. Accepted as written; worth a
  question to the maintainer before claiming.

## Regression (2026-10-09)

Full Unit 1 harness run on the patched rubric (`python3 run_eval.py --rubric issue-scout/rubric.md
--skill issue-scout/SKILL.md --save-run ...`, Sonnet pinned, rubric sha256 2db1a55340c444aa):
19/20, PASS. That is identical to my Unit 1 run: the same category tallies (claimed 4/4,
clear-accept 8/8, dead-repo 3/3, policy 1/1, scope 3/4) and the same single miss, issue-15, the
arguable scope call I chose not to chase in Unit 1. issue-12, the policy reject whose ban sits on
a linked docs page, still rejects, so P2's live-mode wording did not leak into eval mode. Run file:
`eval-run-unit5.txt`.
