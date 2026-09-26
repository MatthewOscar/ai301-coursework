# Plan: Add Mock GitHub API Server for Integration Tests (Issue #22)

## Diagnosis

I read #22 as a test coverage gap plus a CI exemption shim, not a defect in `GitHubTool`'s HTTP handling.

In my inspection of the repository at tree `72cec8e` (commit `f89c06f` in section 1 and commit `2f4e82f` in section 3), I confirmed the following facts:
1. `tests/integration/` holds only a 0-byte `__init__.py`. No integration test exists in the repository to exercise `agent/tools/github_tool.py`.
2. In `agent/tools/github_tool.py`, `self.base_url = "https://api.github.com"` is defined as a plain instance attribute in `__init__` (line 24) rather than a constructor parameter.
3. In `agent/tools/github_tool.py`, `execute()` calls `_fetch_repo_metadata` at line 42:
   - Line 80 performs a GET request to `{self.base_url}/repos/{user}/{repo}`, and line 81 calls `response.raise_for_status()`, raising `httpx.HTTPStatusError` on non-2xx responses.
   - In `execute()` (lines 45-58), the error is caught via `except httpx.HTTPStatusError as e:`:
     - If `e.response.status_code == 404`, it returns `ToolResult(success=False, data={}, error="Repository not found")`.
     - If `e.response.status_code == 403`, it returns `ToolResult(success=False, data={}, error="Rate limited or access denied")` (one combined branch for both rate limiting and permission denial; rate-limit headers are never inspected).
     - Any other status returns `ToolResult(success=False, data={}, error=f"GitHub API error: {e.response.status_code}")`.
   - On HTTP 200, line 94 calls `_has_readme`, which issues an `httpx.head` request to `{self.base_url}/repos/{user}/{repo}/readme` (line 126). `has_readme` evaluates to `True` only when the HEAD returns 200 (line 127); any exception or other status is swallowed to `False`.
   - Because 404 and 403 raise at line 81 during the repository GET prior to line 94, error branches make exactly one HTTP request and never trigger a README lookup.
   - `execute()` catches all HTTP errors internally and never raises an unhandled exception.
4. In `.github/workflows/ci.yml`, the `test-integration` job's "Run integration tests" step (lines 79-94) executes a shell shim:
   - When pytest collects zero tests, it exits with status code 5.
   - The shim catches exit code 5 and translates it to 0, emitting `::notice::No integration tests collected yet - treating as success.`.
   - An inline comment reads: `# Delete this shim once the directory holds at least one test.`

My seam run confirmed that pointing `tool.base_url` at a local server successfully routes requests: 404 returns `ToolResult(success=False, data={}, error='Repository not found')`, 403 returns `ToolResult(success=False, data={}, error='Rate limited or access denied')`, and 200 returns `ToolResult(success=True, ...)` with `has_readme = True`. The tool's HTTP handling already works as intended, but no automated integration tests run against these paths, and CI currently masks the empty test directory.

## Reproduction Evidence

I build the identical diff for both section copies. Below, I quote each section's own runs, labelled by section. The `seam_check.py` script is byte-identical across both sections, so I quote its source once under Section 1.

### 1. Section 1 (`codepath/pathreview-ai301-fa26-s1`, Fork at `f89c06f`)

#### A. My Unit 2 Reproduction (Unposted Upstream)

The following commands and outputs are quoted directly from my Unit 2 reproduction report, which I drafted and gated but did not post upstream:

**Environment:**
- macOS 26.6.1 (arm64)
- Python 3.13.14 (CI pins Python 3.11; `docs/SETUP.md` lists 3.11 as minimum)
- pytest 9.1.1, pytest-httpserver 1.1.5, installed via `.venv/bin/pip install -e ".[dev]"`
- Fork of `codepath/pathreview-ai301-fa26-s1`, `main` at `f89c06f` (tree `72cec8e`)

**Step 1: Contents of `tests/integration/`**
```
$ ls -la tests/integration/
total 0
-rw-r--r--@ 1 matthewoscar  staff    0 Sep 19 18:03 __init__.py
```

**Step 2: Integration suite with CI pytest arguments**
```
$ .venv/bin/python -m pytest tests/integration -v --tb=short
collecting ... collected 0 items

============================ no tests ran in 0.09s =============================
$ echo $?
5
```

**Step 3: Integration suite with `make test-integration` arguments (`-m integration`)**
```
$ .venv/bin/python -m pytest tests/integration -v -m integration
collecting ... collected 0 items

============================ no tests ran in 0.05s =============================
$ echo $?
5
```

**Step 4: Unit suite in the same environment**
```
$ .venv/bin/python -m pytest tests/unit -q
375 passed, 53 xfailed, 1 warning in 5.16s
```

**Step 5: CI exit code 5 shim (`.github/workflows/ci.yml`, lines 79-94)**
```yaml
      - name: Run integration tests
        # tests/integration/ is currently empty by design -- the integration
        # suite is one of the things students contribute. pytest exits 5 on
        # "no tests collected", which would fail this job unconditionally and
        # for a reason unrelated to the PR, so 5 is treated as success here.
        # Delete this shim once the directory holds at least one test.
        run: |
          set +e
          pytest tests/integration -v --tb=short
          code=$?
          set -e
          if [ "$code" -eq 5 ]; then
            echo "::notice::No integration tests collected yet - treating as success."
            exit 0
          fi
          exit "$code"
```

**Step 6: Unit 2 seam check (`seam_check.py`)**
I executed this script at the repository root to verify whether `base_url` redirection works without modifying the constructor (byte-identical across both sections):
```python
from pytest_httpserver import HTTPServer
from agent.tools.github_tool import GitHubTool

server = HTTPServer(port=0)
server.start()
tool = GitHubTool()
print("default base_url :", tool.base_url)
tool.base_url = server.url_for("").rstrip("/")     # the only injection point
print("patched base_url :", tool.base_url)

# 404 branch
server.expect_request("/repos/octocat/nope").respond_with_data("", status=404)
r = tool.execute({"github_username": "octocat", "repo_name": "nope"})
print("404 ->", r.success, repr(r.error))

# 403 branch
server.clear()
server.expect_request("/repos/octocat/limited").respond_with_data("", status=403)
r = tool.execute({"github_username": "octocat", "repo_name": "limited"})
print("403 ->", r.success, repr(r.error))

# success path -- note it needs the /readme HEAD too
server.clear()
server.expect_request("/repos/octocat/ok").respond_with_json(
    {"name": "ok", "description": "d", "language": "Python", "stargazers_count": 1,
     "forks_count": 0, "open_issues_count": 0, "pushed_at": "2026-01-01T00:00:00Z",
     "topics": [], "homepage": None})
server.expect_request("/repos/octocat/ok/readme").respond_with_data("", status=200)
r = tool.execute({"github_username": "octocat", "repo_name": "ok"})
print("200 ->", r.success, "has_readme =", r.data.get("has_readme"))
server.stop()
```
Output:
```
$ .venv/bin/python seam_check.py 2>/dev/null
default base_url : https://api.github.com
patched base_url : http://localhost:49318
2026-09-19 18:28:19 [error    ] github_request_failed          repo=nope status=404 username=octocat
404 -> False 'Repository not found'
2026-09-19 18:28:19 [error    ] github_request_failed          repo=limited status=403 username=octocat
403 -> False 'Rate limited or access denied'
2026-09-19 18:28:19 [info     ] github_repo_fetched            language=Python repo=ok stars=1 username=octocat
200 -> True has_readme = True
```

#### B. Today's Fresh Baseline (Python 3.11.14 to Match CI)

I captured this fresh baseline today in a clean worktree at commit `f89c06f` (tree `72cec8e`), using Python 3.11.14 to match CI. The decisive output lines under each command are:

```
# s1, before
commit: f89c06f
Python 3.11.14 | pytest 9.1.1 | pytest-httpserver 1.1.5

$ ls -la tests/integration/
-rw-r--r--@ 1 matthewoscar  staff    0 Sep 26 00:04 __init__.py

$ .venv/bin/python -m pytest tests/integration -v --tb=short; echo "exit=$?"
collecting ... collected 0 items
no tests ran in 0.46s
exit=5

$ .venv/bin/python -m pytest tests/integration -v -m integration; echo "exit=$?"
collecting ... collected 0 items
no tests ran in 0.05s
exit=5

$ .venv/bin/python -m pytest tests/unit -q
375 passed, 53 xfailed, 4 warnings in 11.66s

$ PATH=.venv/bin:$PATH bash --noprofile --norc -eo pipefail ci-step.sh; echo "exit=$?"
collecting ... collected 0 items
no tests ran in 0.05s
::notice::No integration tests collected yet - treating as success.
exit=0

$ .venv/bin/python seam_check.py 2>/dev/null
404 -> False 'Repository not found'
403 -> False 'Rate limited or access denied'
200 -> True has_readme = True
```

### 2. Section 3 (`codepath/pathreview-ai301-fa26-s3`, Fork at `2f4e82f`)

#### A. My Unit 2 Reproduction (Unposted Upstream)

My Unit 2 reproduction report for the section 3 fork recorded the following environment and seam check execution:

**Environment:**
- macOS 26.6.1 (arm64)
- Python 3.13.14 (CI pins Python 3.11; `docs/SETUP.md` lists 3.11 as minimum)
- pytest 9.1.1, pytest-httpserver 1.1.5, installed via `.venv/bin/pip install -e ".[dev]"`
- Fork of `codepath/pathreview-ai301-fa26-s3`, `main` at `2f4e82f` (tree `72cec8e`)

**Seam Check Output (`seam_check.py`):**
Because `seam_check.py` is byte-identical across both sections, I quote only its section 3 execution output here:
```
$ .venv/bin/python seam_check.py 2>/dev/null
default base_url : https://api.github.com
patched base_url : http://localhost:49327
2026-09-19 18:28:26 [error    ] github_request_failed          repo=nope status=404 username=octocat
404 -> False 'Repository not found'
2026-09-19 18:28:26 [error    ] github_request_failed          repo=limited status=403 username=octocat
403 -> False 'Rate limited or access denied'
2026-09-19 18:28:26 [info     ] github_repo_fetched            language=Python repo=ok stars=1 username=octocat
200 -> True has_readme = True
```

#### B. Today's Fresh Baseline (Python 3.11.14 to Match CI)

I captured this fresh baseline today in a clean worktree at commit `2f4e82f` (tree `72cec8e`), using Python 3.11.14 to match CI. The decisive output lines under each command are:

```
# s3, before
commit: 2f4e82f
Python 3.11.14 | pytest 9.1.1 | pytest-httpserver 1.1.5

$ ls -la tests/integration/
-rw-r--r--@ 1 matthewoscar  staff    0 Sep 26 00:04 __init__.py

$ .venv/bin/python -m pytest tests/integration -v --tb=short; echo "exit=$?"
collecting ... collected 0 items
no tests ran in 0.41s
exit=5

$ .venv/bin/python -m pytest tests/integration -v -m integration; echo "exit=$?"
collecting ... collected 0 items
no tests ran in 0.05s
exit=5

$ .venv/bin/python -m pytest tests/unit -q
375 passed, 53 xfailed, 3 warnings in 10.23s

$ PATH=.venv/bin:$PATH bash --noprofile --norc -eo pipefail ci-step.sh; echo "exit=$?"
collecting ... collected 0 items
no tests ran in 0.05s
::notice::No integration tests collected yet - treating as success.
exit=0

$ .venv/bin/python seam_check.py 2>/dev/null
404 -> False 'Repository not found'
403 -> False 'Rate limited or access denied'
200 -> True has_readme = True
```

## Scope

### In Scope
- Create integration test file `tests/integration/test_github_tool.py` containing class `TestGitHubTool` with marker `@pytest.mark.integration`.
- Create JSON fixture `tests/fixtures/github_responses/repo_success.json` providing repository metadata fields matching the Unit 2 seam check.
- Edit `.github/workflows/ci.yml` in job `test-integration` to replace the exit code 5 shim and obsolete comment with `run: pytest tests/integration -v --tb=short`.
- Maintain code formatting and style per `docs/CONTRIBUTING.md`: black (100 char limit), ruff, mypy, and Google-style docstrings.
- Apply the identical diff to my forks of `codepath/pathreview-ai301-fa26-s1` (base `f89c06f`) and `codepath/pathreview-ai301-fa26-s3` (base `2f4e82f`), both tree `72cec8e`, on branch `fix/22-github-tool-integration` with commit message `test(agent): add GitHubTool integration tests and remove the empty-suite CI shim`.

### Not in Scope
- No modifications to production code (`agent/tools/github_tool.py` or any other file in `agent/`).
- No modification to `GitHubTool.__init__` to add a `base_url` constructor parameter.
- No parsing of rate-limit headers (`X-RateLimit-*`) or distinction between 403 rate-limiting and 403 permission denial in `GitHubTool`.
- No tests for tools other than `GitHubTool`.
- No modifications to `Makefile` or marker definitions in `pyproject.toml`.
- No changes to CI job runners, dependencies, or other workflow jobs.

## Files and Areas

| File | Action | Description |
|---|---|---|
| `tests/integration/test_github_tool.py` | Create | Class-based integration tests for `GitHubTool` testing 404, 403, and 200/README with `pytest-httpserver`. |
| `tests/fixtures/github_responses/repo_success.json` | Create | Static JSON mock response for the GitHub GET `/repos/{owner}/{repo}` endpoint. |
| `.github/workflows/ci.yml` | Modify | Remove the exit code 5 shim (lines 79-94) in `test-integration`; invoke `pytest tests/integration -v --tb=short` directly. |

## Approach

### 1. Fixture Design
I will create `tests/fixtures/github_responses/repo_success.json` with field values matching my Unit 2 seam check, providing the keys read by `GitHubTool._fetch_repo_metadata`:
```json
{
  "name": "ok",
  "description": "d",
  "language": "Python",
  "stargazers_count": 1,
  "forks_count": 0,
  "open_issues_count": 0,
  "pushed_at": "2026-01-01T00:00:00Z",
  "topics": [],
  "homepage": null
}
```

### 2. Integration Test Implementation
In `tests/integration/test_github_tool.py`, I will define a class `TestGitHubTool` marked `@pytest.mark.integration`.
My test fixture method `github_tool(self, httpserver)` will instantiate `tool = GitHubTool()`, set `tool.base_url = httpserver.url_for("").rstrip("/")`, and return the tool instance.
I will write three test methods to exercise the tool against local endpoints:
1. `test_repository_not_found(self, httpserver, github_tool)`:
   - Configures `httpserver.expect_request("/repos/octocat/nope", method="GET").respond_with_data("", status=404)`.
   - Calls `github_tool.execute({"github_username": "octocat", "repo_name": "nope"})`.
   - Calls `httpserver.check()`.
   - Asserts `result.success is False`, `result.data == {}`, and `result.error == "Repository not found"`.
   - Asserts `len(httpserver.log) == 1`, with request method `"GET"` and path `"/repos/octocat/nope"`, proving no `/readme` request was made.
2. `test_rate_limited_or_access_denied(self, httpserver, github_tool)`:
   - Configures `httpserver.expect_request("/repos/octocat/limited", method="GET").respond_with_data("", status=403)`.
   - Calls `github_tool.execute({"github_username": "octocat", "repo_name": "limited"})`.
   - Calls `httpserver.check()`.
   - Asserts `result.success is False`, `result.data == {}`, and `result.error == "Rate limited or access denied"`.
   - Asserts `len(httpserver.log) == 1`, with request method `"GET"` and path `"/repos/octocat/limited"`, proving no `/readme` request was made.
3. `test_fetch_repo_metadata_success_with_readme(self, httpserver, github_tool)`:
   - Loads fixture with `fixture_data = json.loads((Path(__file__).parent.parent / "fixtures" / "github_responses" / "repo_success.json").read_text())`.
   - Configures `httpserver.expect_request("/repos/octocat/ok", method="GET").respond_with_json(fixture_data)`.
   - Configures `httpserver.expect_request("/repos/octocat/ok/readme", method="HEAD").respond_with_data("", status=200)`.
   - Calls `github_tool.execute({"github_username": "octocat", "repo_name": "ok"})`.
   - Calls `httpserver.check()`.
   - Asserts `result.success is True` and `result.error is None`.
   - Asserts `result.data == {"name": "ok", "description": "d", "primary_language": "Python", "star_count": 1, "fork_count": 0, "open_issues_count": 0, "last_commit_date": "2026-01-01T00:00:00Z", "has_readme": True, "topics": [], "homepage": ""}`.
   - Asserts `len(httpserver.log) == 2`: first request GET `/repos/octocat/ok`, second request HEAD `/repos/octocat/ok/readme`.

The tests I add will call `httpserver.check()` and assert the request logs so that any unmatched request or unexpected call causes an immediate failure. I will write Google-style docstrings on the class and every test method per `docs/CONTRIBUTING.md`.

### 3. CI Workflow Modification
In `.github/workflows/ci.yml`, I will delete the empty-suite shim (lines 79-94) and replace it with direct pytest execution:
```yaml
      - name: Run integration tests
        run: pytest tests/integration -v --tb=short
```
My change preserves the existing runner environment, Python 3.11 version, services, and environment variables.

### 4. Code Quality and Formatting
Before committing, I will run the exact quality checks defined in CI:
- `ruff check .` (ci.yml:20)
- `black --check .` (ci.yml:22)
- `mypy api/ core/ ingestion/ rag/ agent/ safety/ --ignore-missing-imports` (ci.yml:33)
My code will adhere to the 100-character line length limit and all repository conventions.

## Test Plan

I will re-run my Unit 2 steps against the built change; for each, the expected-after is:

1. **Check Directory Contents:**
   - Command: `ls -la tests/integration/`
   - Baseline: only `__init__.py` (0 bytes).
   - Expected: lists both `__init__.py` and `test_github_tool.py`.

2. **Run Integration Suite with CI Arguments:**
   - Command: `pytest tests/integration -v --tb=short`
   - Baseline: collected 0 items, exit code 5.
   - Expected: collected 3 items, 3 passed, exit code 0.

3. **Run Integration Suite with Makefile Arguments:**
   - Command: `pytest tests/integration -v -m integration`
   - Baseline: collected 0 items, exit code 5.
   - Expected: collected 3 items, 3 passed, exit code 0.

4. **Verify Unit Test Suite Integrity:**
   - Command: `pytest tests/unit -q`
   - Baseline: 375 passed, 53 xfailed.
   - Expected: unchanged at 375 passed, 53 xfailed.

5. **Verify New CI Step Execution:**
   - Command: `PATH=.venv/bin:$PATH bash --noprofile --norc -eo pipefail ci-step.sh; echo "exit=$?"` with `ci-step.sh` holding the new step body `pytest tests/integration -v --tb=short`.
   - Baseline: shim prints `::notice::No integration tests collected yet - treating as success.` and exits 0.
   - Expected: `collected 3 items`, `3 passed`, no `::notice::` line, exit=0.
   - Negative Verification: run the same new step body from the unchanged main checkout (whose `tests/integration` holds only `__init__.py`) using the worktree's venv; expected `collected 0 items` and exit=5, which the job now reports as a failure.

6. **Verify Standalone Behavior:**
   - Command: `python seam_check.py 2>/dev/null`
   - Baseline: outputs 404, 403, and 200 lines.
   - Expected: outputs identical three lines (tool runtime behavior unchanged).

7. **Negative Test Check (Scratch Verification):**
   - In my scratch verification, I will temporarily change 'Repository not found' at `agent/tools/github_tool.py:53` to 'Repo not found' without committing, then run `pytest tests/integration -v --tb=short`.
   - Expected: `test_repository_not_found` FAILED, `1 failed, 2 passed`, exit 1.
   - Revert: I will immediately revert with `git checkout -- agent/tools/github_tool.py` and verify `git status --short` shows no `agent/` path. My working tree will remain free of production changes.

8. **Verify Code Quality and Type Checking (CI Exact Commands):**
   - Commands:
     - `ruff check .` (ci.yml:20)
     - `black --check .` (ci.yml:22)
     - `mypy api/ core/ ingestion/ rag/ agent/ safety/ --ignore-missing-imports` (ci.yml:33)
   - Expected: each command exits 0 with no lint, formatting, or typing errors.

## Risks and Unknowns

- **`base_url` Seam Decision:** In `agent/tools/github_tool.py`, `self.base_url = "https://api.github.com"` is assigned directly in `__init__`. In my Unit 2 claim comment, I asked whether assigning `tool.base_url` post-instantiation was acceptable, but I never posted that comment upstream and received no maintainer answer. My plan assigns the attribute in the test fixture because it provides a clean, working seam without altering production code or changing the constructor signature.
- **403 Status Conflation:** `GitHubTool` maps HTTP 403 to `"Rate limited or access denied"` without inspecting `X-RateLimit-*` headers. My tests assert this existing behavior rather than modifying it, as distinguishing rate limits from access denial is outside my scope.
- **Local macOS vs CI Ubuntu Runner and Dependency Versions:** My local runs were on macOS (arm64), whereas GitHub Actions runs on `ubuntu-latest`. What I have not verified yet is how newer dependency releases behave under CI; my local runs used pytest 9.1.1 and pytest-httpserver 1.1.5, while CI installs the latest matching packages at runtime, which I will observe once the pull request runs.
- **Fork Branch CI Execution:** GitHub Actions workflows do not run automatically on branch pushes in my forks. I ran the current step body locally, where its shim turns exit 5 into 0. After the build, I will run the new step body the same way, as well as against a checkout whose `tests/integration` holds only `__init__.py`. The Unit 4 pull request is where CI itself runs the updated workflow.

## Deviations

The plan held. The built change is the fixture, the three tests, and the CI replacement described above, applied as one commit to both forks, and every expected-after in the test plan came out as written. The before and after transcripts for both sections, including the empty-suite CI step (exit 5) and the reverted 404 mutation (`1 failed, 2 passed`, exit 1), are pasted in the Evidence block of `plan-and-implement.md`, next to this file. What the plan left open, and how I decided it while building:

- The fixture load is split over two lines (`fixture_path = ...`, then `fixture_data = json.loads(...)`) to stay inside black's 100-character limit. Same path, same file.
- The fixture method and the three tests carry type annotations, and log entries are unpacked as `request, _ = httpserver.log[n]`. The plan specified neither.
- Docstrings follow the Google style that `docs/CONTRIBUTING.md` asks for (Args and Returns sections), which runs longer than the one-line docstrings in `tests/unit/test_readme_scorer.py`.
- black reformatted the test file once, collapsing lines that fit in 100 characters, before `black --check .` passed.
- The commit carries a short body under the planned subject line.

One gap the build surfaced, left out of scope on purpose: no test covers a README HEAD that answers with something other than 200, so a change to the `== 200` comparison in `_has_readme` would still pass all three tests. A mutation probe during review showed exactly that. This plan scoped the tests to 404, 403, and a 200 with a README, so I will name the gap in the pull request as follow-up coverage rather than widen this change.

Nothing about the approach or the scope changed, so the unposted plan comments for both sections stay accurate as written.
