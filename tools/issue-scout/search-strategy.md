# Search strategy: where your scout looks, and why

<!--
THIS IS THE PART YOU WRITE. The scout in SKILL.md executes whatever
queries and filters you define here. It ships empty on purpose: the
strategy is your judgment about where YOUR first contribution lives,
and a scan without authored queries is just someone else's defaults.

A filled strategy must contain:

1. At least one query in the Queries section. Each query is a real
   `gh search issues` invocation, and each should trace to a sentence
   of your fit profile in scope.md: if you cannot say which sentence
   produced a query, the query is probably not yours. Search breadth
   is the point at this stage: prefer generous limits and let the
   ranking filters do the narrowing.

2. At least one ranking filter in the Ranking filters section. A
   filter is a condition or an ordering over fields the search
   results already carry (last activity date, assignee presence,
   label set, comment count, repo). It must be checkable from the
   result fields alone: if deciding it would need opening the issue
   page, it belongs in your rubric, not here. Ranking is the free
   stage; keep it free. Make at least one filter an ordering, or
   state your tie-break: with drop conditions alone, the scout takes
   the top of whatever order the searches happened to return, and
   the graded candidates stop being traceable to you.

3. The top-N line, kept or deliberately changed.
-->

## Queries

Three queries, one per fit-profile sentence in `scope.md`, each quoted below its query. All three
request the same JSON fields, which are exactly the fields the ranking filters read. OR terms are
separate unquoted arguments on purpose: wrapping a whole OR expression in one pair of quotes makes
gh search for it as a single phrase, which returned 0 results when tested on 2026-10-09.

**Q1.** From "Out in the wild, that means open-source Python projects in the LLM application stack:
RAG and retrieval libraries, ..."

```bash
gh search issues rag OR retrieval OR reranker OR embeddings --label "good first issue" --language python --state open --limit 150 --json title,repository,labels,assignees,commentsCount,updatedAt,createdAt,url,authorAssociation,author
```

**Q2.** From "The area I want to get better at is the AI layer itself: ... faithfulness and relevance
evaluation, prompt and output-parsing behaviour, ..."

```bash
gh search issues evaluation OR faithfulness OR hallucination OR "tool calling" OR "output parser" --label "help wanted" --language python --state open --limit 150 --json title,repository,labels,assignees,commentsCount,updatedAt,createdAt,url,authorAssociation,author
```

**Q3.** From "Test infrastructure counts as much as features: missing fixtures, mocks for an
external API, and flaky or absent integration tests around that stack." (`llm mock` is an implicit
AND, which keeps the test work on the LLM stack; it beat `llm tests --match title` when tested.)

```bash
gh search issues llm mock --language python --state open --limit 150 --json title,repository,labels,assignees,commentsCount,updatedAt,createdAt,url,authorAssociation,author
```

## Ranking filters

Combine the three result sets, drop duplicate URLs, then apply these two filters in order. Day
counts are measured back from the moment the scan runs.

**F1, drop.** Drop a result if ANY of these holds:

- `assignees` is non-empty. (scope.md: "Claims count again.")
- `updatedAt` is more than 90 days before the scan date. (Profile: "I need to actually finish
  inside units 5 to 8.")
- `authorAssociation` is `OWNER`, or `author.login` (present and at least 3 characters) is,
  case-insensitively, the start of the owner part of `repository.nameWithOwner`, as with
  `timothywarner` filing in `timothywarner-org/...`. An owner filing in an org named after
  themselves reads `CONTRIBUTOR` or `COLLABORATOR`, not `OWNER`. (Profile: "an issue the owner
  filed against their own solo repo ... is someone's to-do list.")
- `repository.nameWithOwner` starts with `codepath/`. (scope.md: "The sandbox is behind you.")
- `repository.nameWithOwner`, case-insensitively, contains one of these exact words as a whole
  segment: `event`, `events`, `hackathon`, `hackathons`, `hackday`, `hackdays`, `hack-day`,
  `hack-days`, `chapter`, `final-year`, `academy`, `starter`, `supplemental`, `supplementals`,
  `contribution-drive`, `assignment`, `assignments`. A segment begins at the start of the name or
  right after `-`, `_`, `/` or `.`, and ends at the end of the name or right before `-`, `_`,
  `/`, `.` or a digit. (Profile: "I also want a project with people in it"; an event or course
  repo is a backlog seeded for its own participants, the same reason the `student-project` label
  is dropped above. Each word was seen on such a backlog in scan 2, e.g. mini-jarvis-event#61.
  Add a word only when a scan's saved results show it on one.)
- Any `labels` name, lowercased, equals `question`, `duplicate`, `invalid`, `wontfix`, `blocked`,
  `epic`, `tracking`, `documentation` or `docs`, or contains `llm-generated`, `student-project` or
  `ready-for-agent`. (Profile: "keep out of the search where possible: pure copy-editing ...";
  "prefer issues ... over issues that would first need a design change or maintainer agreement";
  "or one queued for an AI agent".)
- `title`, case-insensitively, contains the whole word `epic`, `tracking`, `roadmap`, `umbrella`,
  `typo` or a word starting `translat`, or starts with `Part of #` or `[Part of #`. (Same two
  profile sentences.)

**F2, order, then drop repeat repos.** Sort the survivors by `commentsCount` ascending, then
`createdAt` newest first, then `url` A to Z (the tie-break, so the order is total). Walk that
order and keep only the first issue from each `repository.nameWithOwner`. The first 5 go to
grading. (Profile: "A large issue I cannot reproduce is worth less to me than a smaller one I can
land": an issue with no thread yet carries no claim and no design debate.) Ordering by `updatedAt`
was tested and rejected: a fresh claim comment bumps `updatedAt`, so newest-updated ranks claimed
threads first.

Executable form of the whole funnel, so a stranger gets the same five. It is two steps, shaped
for a non-interactive permission layer (patched 2026-10-09 after scan 1; see scan-notes.md): no
brace groups, no `cd`, no shell variables, no `jq -f` or `--arg`, and the ranking program on a
single line. Replace `SCRATCH` with the literal absolute path of your scratchpad directory.

Step 1, the three searches, each saved to a file:

```bash
gh search issues rag OR retrieval OR reranker OR embeddings --label "good first issue" --language python --state open --limit 150 --json title,repository,labels,assignees,commentsCount,updatedAt,createdAt,url,authorAssociation,author > SCRATCH/q1.json
gh search issues evaluation OR faithfulness OR hallucination OR "tool calling" OR "output parser" --label "help wanted" --language python --state open --limit 150 --json title,repository,labels,assignees,commentsCount,updatedAt,createdAt,url,authorAssociation,author > SCRATCH/q2.json
gh search issues llm mock --language python --state open --limit 150 --json title,repository,labels,assignees,commentsCount,updatedAt,createdAt,url,authorAssociation,author > SCRATCH/q3.json
```

Step 2, F1 then F2 in one command. The file order is the query order, and `now` is the scan
moment:

```bash
jq -s 'def ts(f): f | fromdateiso8601; ((.[0] | map(.q = "Q1")) + (.[1] | map(.q = "Q2")) + (.[2] | map(.q = "Q3"))) | group_by(.url) | map(.[0] + {qs: (map(.q) | unique)}) | length as $combined | map(select(((.assignees | length) == 0) and ((now - ts(.updatedAt)) <= 90 * 86400) and (.authorAssociation != "OWNER") and ((.author.login // "") as $a | (($a | length) < 3) or ((.repository.nameWithOwner | split("/")[0] | ascii_downcase | startswith($a | ascii_downcase)) | not)) and (.repository.nameWithOwner | startswith("codepath/") | not) and (.repository.nameWithOwner | test("(^|[-_/.])(events?|hackathons?|hack-?days?|chapter|final-year|academy|starter|supplementals?|contribution-drive|assignments?)($|[-_/.0-9])"; "i") | not) and ([.labels[].name | ascii_downcase] | any(test("^(question|duplicate|invalid|wontfix|blocked|epic|tracking|documentation|docs)$|llm-generated|student-project|ready-for-agent")) | not) and (.title | test("\\b(epic|tracking|roadmap|umbrella|typo|translat\\w*)\\b|^\\[?part of #"; "i") | not))) | length as $afterF1 | sort_by([.commentsCount, -ts(.createdAt), .url]) | reduce .[] as $i ({seen: {}, out: []}; if .seen[$i.repository.nameWithOwner] then . else (.seen[$i.repository.nameWithOwner] = true | .out += [$i]) end) | .out | {combined: $combined, after_F1: $afterF1, after_repo_dedupe: length, top: (.[0:5] | map({url, repo: .repository.nameWithOwner, title, commentsCount, createdAt, authorAssociation, surfaced_by: .qs}))}' SCRATCH/q1.json SCRATCH/q2.json SCRATCH/q3.json
```

Report the `combined`, `after_F1` and `after_repo_dedupe` counts with the five, so the funnel is
visible in the read-out.

If GitHub answers with a secondary rate limit, wait a minute and run the queries again; three
searches fit well inside the 30-per-minute search budget on their own.

## How many to grade

Grade the top **5** ranked results per scan.

<!-- Five is the default because grading is the only stage that costs
money: about $0.20 per candidate, so a scan is about $1, and you can
iterate the strategy several times for less than one full regression
run. You need one good issue, not ten. Raising this number is
allowed, but it is a decision you state here in a sentence, with the
cost you are accepting; it is not a number you bump because a scan
disappointed you once. -->
