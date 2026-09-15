# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s1`

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

I work across the full stack and am most comfortable in Python — FastAPI, SQLAlchemy,
pytest — with working JavaScript/TypeScript when a change reaches the frontend. The area I
want to get better at is the AI layer itself: retrieval and reranking, faithfulness and
relevance evaluation, prompt and output-parsing behaviour, and the test scaffolding that
makes any of it verifiable.

Rank accepted issues higher when they touch that AI layer (retrieval, generation,
evaluation, agent tooling, safety) or the test infrastructure around it, and higher again
when the work is substantial enough to describe as real engineering rather than a typo or a
docs tidy. Rank lower when the work is pure copy-editing, or when it is mostly visual
frontend polish.

Against that, I need to actually finish: prefer issues whose reproduction path is visible
from the issue itself — a failing test named, a runnable snippet, an existing seam to inject
a fake at — over issues that would first require a design change or maintainer agreement
before any code could be written. A large issue I cannot reproduce is worth less to me than
a smaller one I can land.
