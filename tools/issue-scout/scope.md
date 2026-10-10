# Scope: where to look, and who is looking

<!--
This is the real-world scope. It replaces the sandbox scope.md in
your installed skill: the field is no longer the Path Review repo,
and the house rules do not travel. Like every scope file, this one is
the skill's field of view: the rubric (rubric.md) decides whether an
issue is GOOD; the scope decides which issues are candidates at all,
and the strategy (search-strategy.md) decides where the scout
actually walks. Live mode only: in eval mode the bundle is the whole
world and this file is ignored.
-->

## Where candidates come from

Any public repository. The sandbox is behind you; the field is now
all of GitHub, and your fit profile below is what keeps that
searchable. Start from repos in languages and tools you named there,
and let the profile steer where you search, not just how results are
ranked.

**Claims count again.** The Path Review house rule does not travel.
Out here a claim comment or an assignee is a stranger who got there
first, and you have no channel to coordinate with them. Read claim
signals exactly as the rubric's checks state them; do not carry the
classroom habit of claiming over someone.

**Claim upstream the moment you commit.** The rule cuts both ways:
the instant you decide to take an issue, say so on the issue itself
(a short comment: what you plan to do and roughly when). You are now
the claim signal someone else's skill will read. Deciding privately
and claiming later is how two strangers do the same work twice.

## Your fit profile

<!-- Paste this forward from your sandbox scope.md, and update it:
it now does more work. Besides ranking the issues your rubric
accepts, it steers WHERE the scout searches: your search-strategy.md
queries should each trace to a sentence here, so name the languages,
tools, and project areas you actually want to work in, and anything
you want to avoid. It still never changes a verdict: fit cannot
rescue an issue your rubric rejects, and cannot sink one it
accepts. -->

I work across the full stack and am most comfortable in Python (FastAPI, SQLAlchemy, pytest),
with working JavaScript/TypeScript when a change reaches a frontend. The area I want to get
better at is the AI layer itself: retrieval and reranking, faithfulness and relevance
evaluation, prompt and output-parsing behaviour, and the test scaffolding that makes any of it
verifiable.

Out in the wild, that means open-source Python projects in the LLM application stack: RAG and
retrieval libraries, LLM evaluation frameworks, and agent or tool-calling frameworks. Test
infrastructure counts as much as features: missing fixtures, mocks for an external API, and
flaky or absent integration tests around that stack.

Rank accepted issues higher when they touch that AI layer (retrieval, generation, evaluation,
agent tooling, safety) or the test infrastructure around it, and higher again when the work is
substantial enough to describe as real engineering rather than a typo or a docs tidy. Rank
lower, and keep out of the search where possible: pure copy-editing, translations, and mostly
visual frontend polish.

Against that, I need to actually finish inside units 5 to 8 (about four weeks): prefer issues
whose reproduction path is visible from the issue itself (a failing test named, a runnable
snippet, an existing seam to inject a fake at) over issues that would first need a design
change or maintainer agreement before any code could be written. A large issue I cannot
reproduce is worth less to me than a smaller one I can land.

I also want a project with people in it: an issue the owner filed against their own solo repo,
or one queued for an AI agent, is someone's to-do list, not an opening for a first
contribution.
