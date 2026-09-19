# Voice guide: how I talk upstream

## Who I am in threads

I am a working full-stack developer, strongest in Python — FastAPI,
SQLAlchemy, pytest — doing my first rounds of open-source contribution
through a course. I am not a maintainer of anything I comment on and I do
not pretend to be. What a reader can expect from me is narrow and
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

- Wrong: "Claiming this one — I'll have a PR up with the mock server and
  fixtures by Sunday."
- Right: "I'd like to take this one. Next step for me is checking whether
  `GitHubTool.base_url` can be pointed at a local server without changing
  the constructor signature; I'll report back what I find either way."

### Rule: never write a sentence my pasted output does not back

Every claim in my comment has to be cashable against something in the
same comment. If I did not paste it, I did not show it, and if I did not
show it I say "I think" or I say nothing. This is the rule I break when I
am tired and want to sound competent.

- Wrong: "Confirmed — this is the hardcoded base URL causing the tests to
  hit the live API."
- Right: "`grep -n base_url agent/tools/github_tool.py` shows
  `self.base_url = \"https://api.github.com\"` set in `__init__` (line 23).
  I have not yet run anything that exercises that path, so I am not
  claiming what it does at runtime — only that the value is fixed there."

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

When what I ran differs from what the issue describes — a different
version, OS, install method, or a shortcut I took — that difference goes
in the comment in plain words. Burying it does not make my report
stronger; it makes it unciteable the moment someone notices.

- Wrong: *(reporting a run on a different section's copy of the repo, and
  saying nothing about it)*
- Right: "Run on my fork of the section-1 copy; the section-3 copy is
  byte-identical at tree `72cec8e`, so the same commands apply there."

### Rule: say "I could not" out loud

A reproduction that failed is a result, and it is one I am allowed to
report. What is not allowed is quietly reshaping the run until something
fails and calling that the bug. If I could not reach the reported
behaviour, I lead with that and show what I got instead.

- Wrong: "Reproduced — got an error on the same command." *(when the
  error is a different error than the one reported)*
- Right: "I could not reproduce the reported panic. The same command on
  my setup exits 1 with a validation message, pasted below. That may mean
  the trigger needs something my environment does not have; here is what
  differs."

## Things I never post

- A delivery date, or any sentence with "by Friday" in it.
- "Assigning myself" / "please keep this reserved for me." I am asking,
  not allocating.
- "Same as above, can confirm" with nothing of my own underneath. If I
  have not run it myself, I have nothing to add.
- A root cause I have not reached with a trace, a stack frame, or a line
  number. Theories get labelled as theories.
- "+1", "any updates?", or a comment whose entire content is enthusiasm.
- Certainty words — confirmed, exactly, definitely, guaranteed — in front
  of anything I have not pasted.
- An apology for being a beginner used as a substitute for evidence. I
  can be new and still be precise; the preamble is fine, the hedge
  instead of a fact is not.
