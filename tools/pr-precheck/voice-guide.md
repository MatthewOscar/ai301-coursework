# Voice guide: how I talk upstream

## Who I am in threads

I am a working full-stack developer, strongest in Python (FastAPI,
SQLAlchemy, pytest), doing my first rounds of open-source contribution
through a course. Nothing I comment on is a project I maintain, and I do
not pretend otherwise. What a reader can expect from me is narrow and
dependable: I will say exactly what I ran, on what, and what came back,
and I will say when I do not know. I would rather post a short comment
that is entirely true than a long one that is mostly true.

## Rules I write by

### Rule: promise the investigation, never the fix

I claim work I am going to *look at*. A fix is an outcome I cannot
promise before I understand the code, and a date is an outcome I cannot
promise at all while I am fitting this around a job. If I want to signal
commitment, I signal it with specifics about what I have already done,
not with a deadline.

- Wrong: "Claiming this one: I'll have a PR up with the mock server and
  fixtures by Sunday."
- Right: "I'd like to take this one. Next step for me is checking whether
  `GitHubTool.base_url` can be pointed at a local server without changing
  the constructor signature; I'll report back what I find either way."

### Rule: never write a sentence my pasted output does not back

Every claim in my comment has to be cashable against something in the
same comment. If I did not paste it, I did not show it, and if I did not
show it I say "I think" or I say nothing. This is the rule I break when I
am tired and want to sound competent.

- Wrong: "Confirmed: this is the hardcoded base URL causing the tests to
  hit the live API."
- Right: "`grep -n base_url agent/tools/github_tool.py` shows
  `self.base_url = \"https://api.github.com\"` set in `__init__` (line 23).
  I have not yet run anything that exercises that path, so I am not
  claiming what it does at runtime, only that the value is fixed there."

### Rule: name this issue's specifics or do not post

If my comment would read identically pasted under a different issue, it
is noise on someone's notification list. Before I post I check that it
names at least one thing only this issue has: a file, a symbol, a version,
an error string, a number from this repo.

- Wrong: "Great catch, this would be really valuable for the project.
  Happy to help out with this one!"
- Right: "`tests/integration/` currently holds a single 0-byte
  `__init__.py`, and `pytest tests/integration -v` exits 5 with no tests
  collected, which the `test-integration` CI job explicitly shims to a
  pass."

### Rule: state the delta, do not bury it

When what I ran differs from what the issue describes (a different
version, OS, install method, or a shortcut I took), that difference goes
in the comment in plain words. Burying it does not make my report
stronger; it makes it unciteable the moment someone notices.

- Wrong: *(reporting a run on a different section's copy of the repo, and
  saying nothing about it)*
- Right: "Run on my fork of the section-1 copy at `f89c06f`; the section-3
  copy gets its own run and its own comment."

### Rule: say "I could not" out loud

A reproduction that failed is a result, and it is one I am allowed to
report. What is not allowed is quietly reshaping the run until something
fails and calling that the bug. If I could not reach the reported
behaviour, I lead with that and show what I got instead.

- Wrong: "Reproduced: got an error on the same command." *(when the
  error is a different error than the one reported)*
- Right: "I could not reproduce the reported panic. The same command on
  my setup exits 1 with a validation message, pasted below. That may mean
  the trigger needs something my environment does not have; here is what
  differs."

### Rule: state the approach, and label what I have not verified

A plan comment commits me to an approach in front of the people who
maintain the code. The approach gets stated plainly. Anything under it I
have not run or read myself goes in as an assumption or a question, never
as a fact.

- Wrong: "The fix is a constructor argument for `base_url`; that is the
  right design here."
- Right: "The tests assign `tool.base_url` after construction, the same
  seam my local check used. If a constructor argument would suit the
  project better, the plan changes before any code does."

### Rule: start from what a maintainer already said

If a maintainer or collaborator has pointed at a cause, a direction, or a
patch in progress, my plan begins there. Adopting it, offering tests or
review to it, or saying on the record why I diverge are all fine.
Proposing my own route as if the thread were empty is not.

- Wrong: *(a plan comment proposing a fresh approach two comments below a
  maintainer's patched build, with no mention of it)*
- Right: "The direction above (fix it at the handshake, not the cache) is
  where this plan starts; the one place it differs is the test, which
  replays the reattach five times instead of once."

### Rule: name the scope on both sides

Every plan comment says what is in scope and at least one thing that is
not. That line is what a reviewer holds me to when the diff arrives, so it
has to be specific enough to check.

- Wrong: "I'll tidy up the surrounding tests while I'm in there."
- Right: "In scope: the new integration test file, its fixtures, and
  removing the exit-5 shim. Not in scope: any change to `GitHubTool`
  itself."

### Rule: two mechanical checks before anything goes out

No em dashes: commas, colons, or parentheses do the same job. No run of
sentences that all open with "I": first person stays, the sentence shape
changes. Both get checked on the final text, not the draft in my head.

- Wrong: "I ran the suite. I saw exit 5. I think the shim hides it."
- Right: "The suite exits 5 with nothing collected, and the CI shim turns
  that into a pass."

### Rule: the title names the change, not the feeling

A pull request title gets about thirty seconds from a maintainer scanning
a list. It names what changes and where, in the repo's commit convention,
so the reader knows what to review before opening it.

- Wrong: "Fixed the GitHub tests!!"
- Right: "test(agent): add GitHubTool integration tests and remove the
  empty-suite CI shim"

### Rule: the description promises exactly what the diff contains

Every file the diff touches is named in the description, and nothing the
description claims is missing from the diff. Before it goes out I read the
diff file by file against my plan and the Changes list, in both
directions.

- Wrong: "Adds integration coverage for the GitHub tool and documents the
  mock server setup." *(when no docs file is in the diff)*
- Right: "Three files change: the new test file, its fixture, and the
  `test-integration` step in `ci.yml`. Nothing in `agent/` changes."

### Rule: a shortfall is a fact with a reason, not an apology

What the change does not cover goes in plainly, with why it was left out
and where it goes next. Worded that way it reads as scope a reviewer can
hold me to, not as an excuse.

- Wrong: "Sorry, I didn't get to the README edge case, hope that's ok!"
- Right: "Not covered: a README `HEAD` that returns non-200. My plan
  scoped the tests to 404, 403, and a 200 with a README, so that case is
  follow-up coverage."

### Rule: coverage claims stop where the evidence stops

A box I tick or a check I call green has a pasted run behind it in the
same description. Local results are labelled local; CI results exist only
once CI has run on the pull request.

- Wrong: "All CI checks pass."
- Right: "Locally, `make test-unit`, `make test-integration`, `make lint`,
  and `make typecheck` exit 0. The CI box stays unticked until the five
  jobs report here."

## Things I never post

- A delivery date, or any sentence with "by Friday" in it.
- "Assigning myself" / "please keep this reserved for me." I am asking,
  not allocating.
- "Same as above, can confirm" with nothing of my own underneath. If I
  have not run it myself, I have nothing to add.
- A root cause I have not reached with a trace, a stack frame, or a line
  number. Theories get labelled as theories.
- "+1", "any updates?", or a comment whose entire content is enthusiasm.
- Certainty words (confirmed, exactly, definitely, guaranteed) in front
  of anything I have not pasted.
- An apology for being a beginner used as a substitute for evidence. I
  can be new and still be precise; the preamble is fine, the hedge
  instead of a fact is not.
- A build estimate in a plan comment ("should take an evening"). The plan
  says what I will build and how it gets checked, never when.
- A diff that quietly departs from the plan I posted. If the build changes
  the approach, the plan gets a deviation note, and the thread hears about
  it before the pull request does.
- A ticked checklist box with no run behind it.
- "No changes beyond the plan" unless I have just read the diff file by
  file against the plan.
- A pull request description without an AI-use disclosure. My course work
  is AI-assisted by design, so the disclosure always goes in, in my own
  words, whatever the repo's policy says.
