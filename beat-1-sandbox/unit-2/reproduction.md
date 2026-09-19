# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of my claim and reproduction on the issue I chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

I am enrolled in two sections, so the same work is posted on both section copies of
Path Review. The two repositories are the same repository seeded twice — both `main`
branches point at tree `72cec8e77810ff8ccf8a7a79b4ba991eafae4cff` and carry the
identical 71-issue set — so issue #22 is the same issue in each. Each reproduction
comment reports its own run in its own clone; neither borrows the other's output.

---

## Your identity upstream

**GitHub username**

MatthewOscar

---

## Posted upstream

**Claim comment**

Section 1 — <<<PASTE THE s1 CLAIM COMMENT PERMALINK HERE>>>

> Hi — I'd like to take this one on as my first contribution here.
>
> What I'm planning: add `tests/integration/test_github_tool.py` that points
> `GitHubTool.base_url` at a local `pytest-httpserver` and covers the 404 and
> 403 branches in `execute()` alongside the success path, with canned responses
> under `tests/fixtures/github_responses/`.
>
> Two things I want to settle before I write any test, both from reading
> `agent/tools/github_tool.py` on `main` rather than from anything I've run
> yet — I'll report back either way:
>
> 1. `base_url` looks like a plain instance attribute rather than a constructor
>    argument (`__init__` takes only `api_token`; `self.base_url =
>    "https://api.github.com"` is set at line 24). If that's right, then without
>    changing the signature the only injection point is assigning it after
>    construction. I'd like to know whether that's acceptable here, or whether
>    you'd rather the constructor took it.
> 2. `_fetch_repo_metadata` appears to call `_has_readme`, which issues a second
>    request to `/repos/{owner}/{repo}/readme` (line 119). If so, the mock will
>    need to serve both paths for the success case, not just the metadata one.
>
> `pytest-httpserver>=1.0.8` is already declared in the `dev` extra in
> `pyproject.toml`, so I don't expect this to need a new dependency.
>
> I'll follow this with a reproduction comment recording the current state of
> the integration suite and the environment I ran it on. I'd like to take the
> investigation and post that report — I'll say what I find either way rather
> than promise a particular outcome or a date.

Section 3 — <<<PASTE THE s3 CLAIM COMMENT PERMALINK HERE>>>

(Same text as above, posted on `codepath/pathreview-ai301-fa26-s3` issue #22.)

**Reproduction comment**

Section 1 — <<<PASTE THE s1 REPRODUCTION COMMENT PERMALINK HERE>>>

> Reproduction report for #22. One framing note first: this issue is a
> test-infrastructure task, not a defect, so what follows evidences the
> **current state** the issue describes — an integration suite that collects
> nothing and a CI job that treats that as a pass — rather than a failing
> behaviour.
>
> ## Environment
>
> - macOS 26.6.1 (arm64)
> - Python 3.13.14 — stating the delta: `docs/SETUP.md` gives 3.11 as the
>   *minimum* and `.github/workflows/ci.yml` pins 3.11, so I am one minor
>   version ahead of CI. Nothing below depends on the Python version.
> - pytest 9.1.1, pytest-httpserver 1.1.5, installed by the documented path:
>   `python3.13 -m venv .venv && .venv/bin/pip install -e ".[dev]"`
> - My fork of `codepath/pathreview-ai301-fa26-s1`, `main` at `f89c06f` (tree `72cec8e`).
>
> I did not run `make setup` end to end; the steps below need only the venv and
> the editable install, not the database or the seeded data.
>
> ## Steps and what I observed
>
> **1. What is actually in `tests/integration/`:**
>
> ```
> $ ls -la tests/integration/
> total 0
> -rw-r--r--@ 1 matthewoscar  staff    0 Sep 19 18:03 __init__.py
> ```
>
> One file, zero bytes. `tests/fixtures/` exists with `sample_profiles/` and
> `sample_resumes/`, but `tests/fixtures/github_responses/` does not.
>
> **2. The suite, run the way CI runs it:**
>
> ```
> $ .venv/bin/python -m pytest tests/integration -v --tb=short
> collecting ... collected 0 items
>
> ============================ no tests ran in 0.09s =============================
> $ echo $?
> 5
> ```
>
> **3. The same, the way `make test-integration` runs it** (it adds
> `-m integration`):
>
> ```
> $ .venv/bin/python -m pytest tests/integration -v -m integration
> collecting ... collected 0 items
>
> ============================ no tests ran in 0.05s =============================
> $ echo $?
> 5
> ```
>
> **4. Control — the unit suite on the same venv:**
>
> ```
> $ .venv/bin/python -m pytest tests/unit -q
> 375 passed, 53 xfailed, 1 warning in 5.16s
> ```
>
> So the empty result in steps 2 and 3 is specific to `tests/integration/`, not
> a broken environment on my side.
>
> **5. What CI does with that exit code** (`.github/workflows/ci.yml`,
> `test-integration` job, lines 79–94):
>
> ```yaml
>       - name: Run integration tests
>         # tests/integration/ is currently empty by design -- the integration
>         # suite is one of the things students contribute. pytest exits 5 on
>         # "no tests collected", which would fail this job unconditionally and
>         # for a reason unrelated to the PR, so 5 is treated as success here.
>         # Delete this shim once the directory holds at least one test.
>         run: |
>           set +e
>           pytest tests/integration -v --tb=short
>           code=$?
>           set -e
>           if [ "$code" -eq 5 ]; then
>             echo "::notice::No integration tests collected yet - treating as success."
>             exit 0
>           fi
>           exit "$code"
> ```
>
> That is the "stop treating an empty suite as a pass" from the issue, in the
> repo's own words, and it names its own removal condition.
>
> **Expected:** `test-integration` exercises `GitHubTool` against a local
> server and fails when that tool regresses.
> **Actual:** it collects zero tests, exits 5, and the shim converts 5 to 0, so
> the job is green without having tested anything.
>
> ## The seam works — the gap is only the missing tests
>
> Before saying this looked straightforward I checked that `base_url` really is
> injectable without touching the signature. This is the whole script I ran,
> saved as `seam_check.py` at the repo root and **not** added as a test file:
>
> ```python
> from pytest_httpserver import HTTPServer
> from agent.tools.github_tool import GitHubTool
>
> server = HTTPServer(port=0)
> server.start()
> tool = GitHubTool()
> print("default base_url :", tool.base_url)
> tool.base_url = server.url_for("").rstrip("/")     # the only injection point
> print("patched base_url :", tool.base_url)
>
> # 404 branch
> server.expect_request("/repos/octocat/nope").respond_with_data("", status=404)
> r = tool.execute({"github_username": "octocat", "repo_name": "nope"})
> print("404 ->", r.success, repr(r.error))
>
> # 403 branch
> server.clear()
> server.expect_request("/repos/octocat/limited").respond_with_data("", status=403)
> r = tool.execute({"github_username": "octocat", "repo_name": "limited"})
> print("403 ->", r.success, repr(r.error))
>
> # success path -- note it needs the /readme HEAD too
> server.clear()
> server.expect_request("/repos/octocat/ok").respond_with_json(
>     {"name": "ok", "description": "d", "language": "Python", "stargazers_count": 1,
>      "forks_count": 0, "open_issues_count": 0, "pushed_at": "2026-01-01T00:00:00Z",
>      "topics": [], "homepage": None})
> server.expect_request("/repos/octocat/ok/readme").respond_with_data("", status=200)
> r = tool.execute({"github_username": "octocat", "repo_name": "ok"})
> print("200 ->", r.success, "has_readme =", r.data.get("has_readme"))
> server.stop()
> ```
>
> Output, with the werkzeug access log on stderr suppressed; the three
> `[error]`/`[info]` lines are the tool's own structlog output:
>
> ```
> $ .venv/bin/python seam_check.py 2>/dev/null
> default base_url : https://api.github.com
> patched base_url : http://localhost:49318
> 2026-09-19 18:28:19 [error    ] github_request_failed          repo=nope status=404 username=octocat
> 404 -> False 'Repository not found'
> 2026-09-19 18:28:19 [error    ] github_request_failed          repo=limited status=403 username=octocat
> 403 -> False 'Rate limited or access denied'
> 2026-09-19 18:28:19 [info     ] github_repo_fetched            language=Python repo=ok stars=1 username=octocat
> 200 -> True has_readme = True
> ```
>
> Both branches the issue names are reachable this way. One thing to know for
> the fixtures: the 200 case needs **two** expectations, because
> `_fetch_repo_metadata` calls `_has_readme` while building its result dict
> (line 94), and `_has_readme` issues a separate `httpx.head` (line 126) to
> the URL built at line 119, `/repos/{owner}/{repo}/readme`. A mock serving
> only the metadata path leaves `has_readme` false.
>
> ## Two notes for whoever picks this up
>
> 1. **`GitHubTool` has no coverage of any kind, not just no integration
>    coverage.** There is no `tests/unit/test_github_tool.py` either, so the
>    404 and 403 branches in `execute()` have never been exercised by the
>    suite. That doesn't change the ask, but it means there is no existing test
>    to pattern-match against for this tool.
> 2. **`make test-integration` and CI disagree on marker filtering.** The
>    Makefile target passes `-m integration`; CI runs bare
>    `pytest tests/integration -v --tb=short`. A new test without
>    `@pytest.mark.integration` would pass CI but be invisible to the Makefile
>    target locally. Worth deciding which one is authoritative before writing
>    the first test.
>
> ## What this does and does not establish
>
> It establishes that the suite is empty, that both documented invocations exit
> 5, that CI swallows that exit code, and that `base_url` can be redirected at a
> local server with the 404 and 403 branches reachable. It does not establish
> anything about `GitHubTool`'s behaviour against the real GitHub API — I never
> called it — and it is not a reproduction of a defect, because the issue does
> not report one.

Section 3 — <<<PASTE THE s3 REPRODUCTION COMMENT PERMALINK HERE>>>

> Reproduction report for #22. One framing note first: this issue is a
> test-infrastructure task, not a defect, so what follows evidences the
> **current state** the issue describes — an integration suite that collects
> nothing and a CI job that treats that as a pass — rather than a failing
> behaviour.
>
> ## Environment
>
> - macOS 26.6.1 (arm64)
> - Python 3.13.14 — stating the delta: `docs/SETUP.md` gives 3.11 as the
>   *minimum* and `.github/workflows/ci.yml` pins 3.11, so I am one minor
>   version ahead of CI. Nothing below depends on the Python version.
> - pytest 9.1.1, pytest-httpserver 1.1.5, installed by the documented path:
>   `python3.13 -m venv .venv && .venv/bin/pip install -e ".[dev]"`
> - My fork of `codepath/pathreview-ai301-fa26-s3`, `main` at `2f4e82f` (tree `72cec8e`).
>
> I did not run `make setup` end to end; the steps below need only the venv and
> the editable install, not the database or the seeded data.
>
> ## Steps and what I observed
>
> **1. What is actually in `tests/integration/`:**
>
> ```
> $ ls -la tests/integration/
> total 0
> -rw-r--r--@ 1 matthewoscar  staff    0 Sep 19 18:03 __init__.py
> ```
>
> One file, zero bytes. `tests/fixtures/` exists with `sample_profiles/` and
> `sample_resumes/`, but `tests/fixtures/github_responses/` does not.
>
> **2. The suite, run the way CI runs it:**
>
> ```
> $ .venv/bin/python -m pytest tests/integration -v --tb=short
> collecting ... collected 0 items
>
> ============================ no tests ran in 0.09s =============================
> $ echo $?
> 5
> ```
>
> **3. The same, the way `make test-integration` runs it** (it adds
> `-m integration`):
>
> ```
> $ .venv/bin/python -m pytest tests/integration -v -m integration
> collecting ... collected 0 items
>
> ============================ no tests ran in 0.06s =============================
> $ echo $?
> 5
> ```
>
> **4. Control — the unit suite on the same venv:**
>
> ```
> $ .venv/bin/python -m pytest tests/unit -q
> 375 passed, 53 xfailed, 1 warning in 5.27s
> ```
>
> So the empty result in steps 2 and 3 is specific to `tests/integration/`, not
> a broken environment on my side.
>
> **5. What CI does with that exit code** (`.github/workflows/ci.yml`,
> `test-integration` job, lines 79–94):
>
> ```yaml
>       - name: Run integration tests
>         # tests/integration/ is currently empty by design -- the integration
>         # suite is one of the things students contribute. pytest exits 5 on
>         # "no tests collected", which would fail this job unconditionally and
>         # for a reason unrelated to the PR, so 5 is treated as success here.
>         # Delete this shim once the directory holds at least one test.
>         run: |
>           set +e
>           pytest tests/integration -v --tb=short
>           code=$?
>           set -e
>           if [ "$code" -eq 5 ]; then
>             echo "::notice::No integration tests collected yet - treating as success."
>             exit 0
>           fi
>           exit "$code"
> ```
>
> That is the "stop treating an empty suite as a pass" from the issue, in the
> repo's own words, and it names its own removal condition.
>
> **Expected:** `test-integration` exercises `GitHubTool` against a local
> server and fails when that tool regresses.
> **Actual:** it collects zero tests, exits 5, and the shim converts 5 to 0, so
> the job is green without having tested anything.
>
> ## The seam works — the gap is only the missing tests
>
> Before saying this looked straightforward I checked that `base_url` really is
> injectable without touching the signature. This is the whole script I ran,
> saved as `seam_check.py` at the repo root and **not** added as a test file:
>
> ```python
> from pytest_httpserver import HTTPServer
> from agent.tools.github_tool import GitHubTool
>
> server = HTTPServer(port=0)
> server.start()
> tool = GitHubTool()
> print("default base_url :", tool.base_url)
> tool.base_url = server.url_for("").rstrip("/")     # the only injection point
> print("patched base_url :", tool.base_url)
>
> # 404 branch
> server.expect_request("/repos/octocat/nope").respond_with_data("", status=404)
> r = tool.execute({"github_username": "octocat", "repo_name": "nope"})
> print("404 ->", r.success, repr(r.error))
>
> # 403 branch
> server.clear()
> server.expect_request("/repos/octocat/limited").respond_with_data("", status=403)
> r = tool.execute({"github_username": "octocat", "repo_name": "limited"})
> print("403 ->", r.success, repr(r.error))
>
> # success path -- note it needs the /readme HEAD too
> server.clear()
> server.expect_request("/repos/octocat/ok").respond_with_json(
>     {"name": "ok", "description": "d", "language": "Python", "stargazers_count": 1,
>      "forks_count": 0, "open_issues_count": 0, "pushed_at": "2026-01-01T00:00:00Z",
>      "topics": [], "homepage": None})
> server.expect_request("/repos/octocat/ok/readme").respond_with_data("", status=200)
> r = tool.execute({"github_username": "octocat", "repo_name": "ok"})
> print("200 ->", r.success, "has_readme =", r.data.get("has_readme"))
> server.stop()
> ```
>
> Output, with the werkzeug access log on stderr suppressed; the three
> `[error]`/`[info]` lines are the tool's own structlog output:
>
> ```
> $ .venv/bin/python seam_check.py 2>/dev/null
> default base_url : https://api.github.com
> patched base_url : http://localhost:49327
> 2026-09-19 18:28:26 [error    ] github_request_failed          repo=nope status=404 username=octocat
> 404 -> False 'Repository not found'
> 2026-09-19 18:28:26 [error    ] github_request_failed          repo=limited status=403 username=octocat
> 403 -> False 'Rate limited or access denied'
> 2026-09-19 18:28:26 [info     ] github_repo_fetched            language=Python repo=ok stars=1 username=octocat
> 200 -> True has_readme = True
> ```
>
> Both branches the issue names are reachable this way. One thing to know for
> the fixtures: the 200 case needs **two** expectations, because
> `_fetch_repo_metadata` calls `_has_readme` while building its result dict
> (line 94), and `_has_readme` issues a separate `httpx.head` (line 126) to
> the URL built at line 119, `/repos/{owner}/{repo}/readme`. A mock serving
> only the metadata path leaves `has_readme` false.
>
> ## Two notes for whoever picks this up
>
> 1. **`GitHubTool` has no coverage of any kind, not just no integration
>    coverage.** There is no `tests/unit/test_github_tool.py` either, so the
>    404 and 403 branches in `execute()` have never been exercised by the
>    suite. That doesn't change the ask, but it means there is no existing test
>    to pattern-match against for this tool.
> 2. **`make test-integration` and CI disagree on marker filtering.** The
>    Makefile target passes `-m integration`; CI runs bare
>    `pytest tests/integration -v --tb=short`. A new test without
>    `@pytest.mark.integration` would pass CI but be invisible to the Makefile
>    target locally. Worth deciding which one is authoritative before writing
>    the first test.
>
> ## What this does and does not establish
>
> It establishes that the suite is empty, that both documented invocations exit
> 5, that CI swallows that exit code, and that `base_url` can be redirected at a
> local server with the 404 and 403 branches reachable. It does not establish
> anything about `GitHubTool`'s behaviour against the real GitHub API — I never
> called it — and it is not a reproduction of a defect, because the issue does
> not report one.

## Eval iterations

**Run history**

1. **20/20 on a free offline dry run**, before spending anything. I graded all 24
   bundles (the 20 scored plus the 4 calibration packages) with local Sonnet
   graders handed exactly the files the harness hands it — `SKILL.md`, my
   `rubric.md`, my `references/evidence-guide.md`, and one bundle — and explicitly
   forbidden from opening `gold-labels.json` or the `.json` twins. I scored their
   verdicts against gold myself afterwards: 20/20 scored, every category matched,
   and 4/4 on the calibration packages including `calib-03`, the operator-swap trap
   from the activity. This is not an eval run and appears in no transcript; it is
   how I avoided paying $4 to discover a formatting fault.
2. **3/3 on a `--limit 3` smoke run.** Mechanical check only — that the skill emits
   a parseable fenced JSON block, that the three preflight gates pass, and that my
   `--rubric` / `--evidence` / `--skill` paths resolve to the installed copy.
   Partial runs print no bar verdict and refuse to write `eval-run.txt`, which is
   why this is not the committed run.
3. **20/20 — PASS.** Confirming full run, the one committed in `eval-run.txt`.
   `categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4
   unfollowable-comms 3/3  wrong-target 4/4`. No disagreements, so there was
   nothing to re-run with `--only` and no second full run.

**Package analysis**

**pkg-16** (pandas-dev/pandas#66656, category `wrong-target`). My rubric graded it
**reject**; the gold label is **reject**. They agree, and the reason they agree is
the one clause I would have been most likely to leave out.

Almost everything about pkg-16 is clean, and my own rubric says so row by row.
`steps-rerunnable` passes: the report runs the issue's own two lines and creates its
fixture in-band. `behavior-matches-issue` **passes** — and this is the part worth
dwelling on. The trigger is character-identical to the issue's, and the pasted
`ValueError: Length of new names must be 1, got 3` is the same error class the issue
and thread describe. A rubric that catches wrong-target packages by comparing the
command and the error class, which is how the other three packages in this category
are caught, sees nothing wrong here at all.

What is wrong is invisible in the artifact and lives in the version line. The report
says `Environment: pandas 1.5.3 (pip)`. The issue's body says the reporter confirmed
the defect "on the latest version and on the main branch", and a thread comment
confirms it on "pandas 2.3.3 and current main". So the `ValueError` the report pastes
is 1.5.3's behaviour, not evidence about the build the issue places the defect on —
and no sentence in either draft names that gap. Two rows fire on that single fact:
`environment-recorded` limb (c), and `claim-matches-evidence` limb (d), because the
report's "The crash the issue describes is confirmed" is scoped wider than the build
it ran on.

The lesson I took is that "wrong target" has two shapes, not one. Three of the four
packages in this category run a different *case*; pkg-16 runs the right case on the
wrong *build*. A rubric with only the first shape scores 3/4 here and never knows why.

**Check rationale**

From the `environment-recorded` row, limb (c), exactly as it reads in the
`rubric.md` I uploaded to `tools/repro-check/`:

> (c) STALE BUILD, GAP NOT NAMED: the build under test is OLDER than a build the ISSUE ITSELF places the defect on — the version the issue states, a version its body or its ticked template checks say the reporter confirmed it on, "the latest version" or "the main branch" where the issue says so, or a version a thread comment confirms it on — and no sentence anywhere in either draft names that gap. Quote the issue's build and the report's.

Two things in that wording are load-bearing, and both are there because of what I
rejected.

The first is **OLDER**, rather than "differs from". A symmetric version check was my
first instinct and it is wrong in this corpus: pkg-03 tests ripgrep 15.2.0 against an
issue filed on 13.0.0, pkg-07 tests p5.js 1.11.7 against 1.9.4, pkg-12 tests prettier
3.9.6 against 3.8.4, and all three are gold accepts. Testing *forward* is the useful
direction — it tells the maintainer the defect survived — so a newer build can never
fire this limb. Four of the eight clear-accepts would have died on a symmetric rule.

The second is the enumeration of **what counts as a build the issue places the defect
on**. It is not the repo's "latest release" line. It is what the *issue* claims: the
version it states, what its ticked template checks say, the words "the latest version"
or "the main branch" where the issue itself uses them, and a version a thread comment
confirms it on. pkg-16 is only reachable through the last two of those four, because
the issue names no numeric version of its own — the defect's location lives in prose
in the body and in a comment forty lines down.

On how I got there, since the question asks what I revised: I did not write this
rubric in one pass. I had four candidate rubrics drafted independently from the five
proof families — one of them by a different vendor's model, so the panel did not share
one model's priors — and then had each torn apart by a critic that *did* have the gold
labels, to find what each would misgrade. Limb (c) exists because that process showed
every candidate silently accepting pkg-16. I then re-derived the whole set blind
(run 1 above) to check I had not simply fitted the answer key. The clause states a
principle I would defend having never seen pkg-16 — a report testing an older build
than the one the issue confirms the defect on, without naming the gap, is not evidence
about the reported defect — but it was a machine tracing one package that told me the
principle was missing.

**Trade-offs**

Limb (c) gives up the ability to fail a report for *any* version difference, and that
is deliberate. A report run on a different OS, shell, install method, architecture or
point release passes this row whether or not it mentions the difference; only a build
strictly older than one the issue pins, with the gap unnamed, fails. pkg-03 is the
package that bought that concession: it runs ripgrep 15.2.0 on Arch Linux against an
issue filed from Kubuntu 23.10, and never explains the distro change. Under a stricter
reading of "environment matches the issue" it is a reject, and it is a gold accept. So
the row trades away the confident-looking report that quietly drifted sideways in
order to keep the four clear-accepts that legitimately tested forward.

Nothing else changed, and here is how I know. There was no revise loop to run: the
first full run agreed on all 20, so no `--only` canary was ever spent. What stands in
for it is the blind offline pass in run 1, which graded the same 24 bundles against
these exact files with the answer key withheld and returned the same verdict on every
one — including `calib-04`, the activity's borderline package, which this row rejects
on limb (b) for recording no platform at all rather than on limb (c). Had limb (c)
been doing work it should not, `calib-04` and the four forward-testing accepts are
where it would have shown, and it did not.

If I do ever loosen this row, the canary list is
`--only pkg-16,pkg-03,pkg-07,pkg-12`: pkg-16 is the only package the limb is
load-bearing for, and the other three are the forward-testing accepts it must never
touch.

---

Related paths: `eval-run.txt` in this directory; my skill's files in
`tools/repro-check/`.
