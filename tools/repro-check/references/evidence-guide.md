# Evidence guide: where proof lives in a reproduction package

Every rubric check names evidence. This guide is the map: for each of the
five proof families, where that evidence sits in an eval bundle, where it
sits live (on GitHub and in the student's drafts), and what good looks
like when you get there — stated as conditions someone else could apply
rather than as adjectives.

Two rules govern the whole map. First, a package is what the claim
comment and the repro report CONTAIN AND QUOTE, read against the issue
context and the repo-facts block in the same bundle. Files in the
student's working directory, a run that happened but was not written
down, and another commenter's screenshot are not part of the package — but
what the ISSUE itself pastes is, because every reader of a comment has the
issue open above it. A stranger on the thread sees the two drafts and the
issue; so do you. Second, artifacts decide and prose does not: a pasted
command, output block, log or capture is evidence, and a sentence about
one is not, however confident and however well formatted.

One premise travels with this guide and is not a finding you look for:
packages this skill grades are AI-assisted work unless the package itself
says otherwise. That premise settles only whether a disclosure sentence
was owed under a policy that asks for one. It never tells you who typed a
given sentence, and no amount of style is evidence of authorship.

## Environment

**Where it lives.** In an eval bundle: the repro report's environment
record — usually its first line or a block headed "Environment", naming
version, OS and install method; sometimes an inline artifact such as a
pasted version-command output; sometimes a sentence in the claim comment
("reproduced on current 0.64.1 on Linux"). Read it against three other
places in the same bundle. (1) The issue context's own environment fields
— its "Version:", "Operating system:" and "installed via" lines, or a
quoted installed-versions block. (2) Anything in the issue that places the
defect on a build: a sentence saying the reporter confirmed it on the
latest version and on the main branch, a ticked template check the issue
reports, a bisected commit, a statement that the build came from a branch.
(3) The thread, where a maintainer or a commenter often confirms the defect
on a second, usually newer, version. The repo-facts "latest release" line
tells you where the current release line sits; it is context, never the
target. Live mode: the student's draft repro comment for the candidate
side; the issue body's environment section, the issue's labels, and any
maintainer comment naming a version or build for the target side; the
repo's Releases sidebar for what "latest" means today.

**What good looks like.** The record names, for the reporter's own run,
WHAT was run (version, build, tag, commit, or a hosted instance identifier
where the tool ships no version to its user) and WHERE it ran (operating
system or platform), so a maintainer can tell whose machine produced the
artifact. Thin is not insufficient: one line naming a version, an install
method and an OS is a complete record, and a missing kernel, architecture,
dependency list or build profile is a courtesy rather than a gap. Prose
counts as fully as a pasted version dump.

The comparison that matters is narrow. Ask only: is this build OLDER than
a build the ISSUE places the defect on? Testing the same build, or a newer
one, is fine and needs no apology — drifting toward the current release is
the useful direction, and "the issue was filed against 13.0.0, behavior is
unchanged on 15.2.0" is a better record than a match. Testing OLDER, on an
issue whose reporter confirmed the defect on latest and on main and whose
thread confirms it on a newer release again, is a different experiment
from the one the maintainers asked about, and a package that calls that
"confirmed" without naming the gap has answered a question nobody asked.

Two things that look like defects and are not. An OS, distribution or
shell that differs from the issue's, on an issue that does not scope the
defect to a platform, is not a deviation at all: the issue's environment
block describes the reporter's machine, it does not pin an axis, and
running elsewhere is coverage. And the only version facts that count are
this reporter's own — a version and OS that belong to another commenter's
screenshot or to the issue reporter's own machine record nothing about
this run, which is the one shape that makes an apparently populated
environment line worthless.

## Steps

**Where it lives.** In an eval bundle: the repro report's steps or
procedure block — numbered steps, a shell transcript with prompts, a
described UI sequence, or a paragraph naming what was run — plus whatever
the same report pastes as output, which is the cross-check, because an
output line can prove a step the written procedure never performs. Read it
with two other sources open. The issue context, because inputs the issue
quotes verbatim — a fixture's exact lines, an exact input string with its
offsets, an exact style block, an exact config — are in front of every
reader of the comment and need not be pasted again. And the thread, where
someone has often already said what a substitute payload cannot do. Live
mode: the student's draft repro comment; the issue body's reproduction
section and any attached gist, playground link or file.

**What good looks like.** A stranger with the same tool could re-run the
decisive run and land in the same place, using the package plus the issue
it is posted under. Concretely: the starting state is created in-band or
is the project's ordinary state; the trigger is an actual command or an
actual keystroke sequence rather than a paraphrase of the issue's
hypothesis; and every input the outcome depends on is reachable — pasted
in the report, quoted in the issue, linked, or fixed by a stated property
that any input carrying it would satisfy ("a valid dependencies list plus
a category section, the section conda does not recognize" determines the
input well enough that any file meeting it reproduces).

Reachability, not pasting, is the test. A wrapper script the report
describes by naming every parameter the issue itself pastes — its input
strings, its offsets, its options, its parser — is reconstructible byte for
byte by anyone reading the comment, and it passes however loosely the
report names the file it put those parameters in. What does NOT pass is an
input whose bytes exist nowhere a reader can go: a private monorepo, an
internal config, an unshared block list, a payload described only by size,
or an input the thread already says a synthetic substitute cannot
reproduce.

Followable does not mean long. Two shell lines that create their own
fixture and run the command are a complete procedure. A hand-performed
step written in prose is a step, because TUI, GUI and browser bugs have no
other form. A variation the report mentions but does not paste leaves the
row intact as long as the run behind the pasted artifact is complete.
Three things break it and each is quotable: no procedure at all; an input
the package declares unshareable, so the positive result lives where
nobody can check it; and the report's own pasted output naming a resource
its steps never create, which proves the written steps do not reach the
failing path.

## Behavior shown

**Where it lives.** In an eval bundle: the artifacts inside the repro
report — fenced output blocks, error text, panic traces, exit codes, log
excerpts, a described capture, a measurement — and the one or two sentences
(usually an "Actual:" line) that say what those artifacts mean. The
comparison target is the issue context: its exact trigger string, its exact
error text or described observable, its error class, its exit code where it
names one, and its own control or contrast case where it supplies one. The
thread matters too, because a maintainer often restates the real
discriminator or adds a required flag the issue body omitted. Live mode:
the student's draft report for the artifact; the issue body, its
attachments and the maintainer comments for the target — and note that
someone ELSE's screenshot in the thread is their evidence, never this
package's.

**What good looks like.** Read the tested CASE against the issue's before
you read the outputs against each other. The strongest packages let you say
that the two invocations are character-identical and the two outputs are
too — the same error string, the same missing header line, the same wrong
line numbers, the same reply bytes. Where the report changed something, the
change is visible in its own text and preserves what is under test:
printing the constructed request offline instead of over the network, a
sibling entrypoint of the same tool, a minimal local file standing in for a
remote URL. Going beyond the issue is a strength, never a deviation: a
differential run that removes the suspected flag, a pair differing in one
character of payload, a second tool re-parsing the output to prove it does
not parse.

An artifact shows an ADJACENT behavior, not the issue's, when any of these
is quotable: the trigger differs on something the issue's own text, its
control case or a maintainer's correction identifies as deciding the
outcome — a moved separator, a deleted sign, a reversed range direction, a
silently dropped flag, a replaced expression body, a platform the issue
scopes itself to and the report never establishes; the outcome is a
different class — a clean validation error where the issue shows a panic
and a specific exit code, a surviving terminal where the issue reports the
process dying, a compile-time error where the issue shows a runtime one, a
command that genuinely failed where the issue reports one that succeeded
and was misread; every artifact is setup state (a version string, a session
list, tabs visible in a tab bar) while the failing observable never
appears; there is no artifact at all and the evidentiary content is an
assertion, possibly leaning on another commenter's capture; or the pasted
outcome is the graceful rejection or clamp the issue asks for as its
REMEDY, narrated as the defect.

And an artifact showing that the failure did NOT occur is a legitimate
artifact, provided the prose says so — see Honesty.

## Honesty

**Where it lives.** At the seam between two things already on the map: the
repro report's claim sentences (its Actual line, its summary sentence,
anything containing confirm, reproduced, verified, root cause, exactly,
definitely, conclusively, a percentage, or "every time") and the artifacts
pasted a few lines above them. The claim comment carries the same claims
forward to the thread, so read it as part of the same seam — the comment's
promise and the report's evidence must be the same size. Live mode: the
student's two drafts read against each other and against the issue
discussion, before either is posted; use the text the maintainer will
receive, because private confidence and an unshared experiment do not
expand the demonstrated result.

**What good looks like.** Every sentence about the outcome maps to
something pasted, and sentences that go past the evidence are marked as
leads rather than findings. Honest looks like an Actual line whose
parenthesis points at the artifact ("the warning is printed to stdout above
the JSON, shown above, so the stream does not parse"). Honest also looks
like a negative: a report leading with "I could NOT reproduce scenario 2",
followed by the log that shows the non-event and a sentence naming the
unmet precondition; or "cannot reproduce on Linux and zsh with the
report's exact layout and config", with both observables the issue names
actually checked and the environment delta stated. An evidenced
cannot-reproduce is the most honest shape a package can take and is ready
to post.

Dishonest is the inverse and is always quotable, in four shapes. A
reproduction asserted where no artifact shows the failure. A cause stated
as established where nothing in the package reaches that code path — no
trace, no line number, no instrumented or differential run. A fidelity
claim ("ran it verbatim", "following the report's syntax") sitting next to
a difference you can quote. And an unqualified "confirmed" about a build
older than the one the issue places the defect on, with the gap never
named. Two tells worth knowing because they travel together: certainty
adverbs multiplying as evidence thins, and repetition offered as proof of a
deterministic error.

What is NOT dishonesty, and this list is what keeps honest terse packages
alive: a bare repetition or extra-machine count, which says nothing about
what happened; an unshown variation sweep; a scope word true of the run
actually pasted while a neighbouring value went untested; a hedged
mechanism offered as a next step; a cause attributed to the thread or a
maintainer; a restatement of what the issue itself asks for; a wrong guess
at why one's own reproduction failed; a loose name for a wrapper built
entirely from the issue's own pasted parameters; and confidence in a report
whose every claim is backed.

## Comms

**Where it lives.** Two separate surfaces, judged separately. The claim
comment: in an eval bundle, its own section, read against the issue context
it would be posted under (title, symptom wording, version, flags, file and
symbol names, and whatever the thread's maintainers have already said) and
against the repro report attached below it. The repo's conventions: the
repo-facts block's contribution-policy line, including any quoted
CONTRIBUTING, AI_POLICY or AI_USAGE_POLICY text and the section name it
cites, read against the full text of BOTH drafts — and in eval mode that
line is the whole policy world, so if it says "no stated AI policy" there
is no policy, whatever the repo may have elsewhere. Live mode: the
student's draft claim comment and the issue thread with its
author-association badges; CONTRIBUTING.md in the repo root or .github/,
plus any policy file it links out to, plus the issue and PR templates.

**What good looks like, for the claim comment.** It could only have been
written under this issue: it names the version tested, the exact symptom or
error string, a flag, file, function or subsystem, or it picks up a pointer
someone in the thread already gave, and it states a next step concrete
enough to be checkable. Its promise is the size of its evidence — "I'd like
to investigate", "I'll report back what I find", "before opening a PR" —
not a fix, not a date, and not a demand that the issue be reserved or an
announcement that it is now assigned. Carrying a negative result into the
claim is a strength, not a weakness. Brevity is fine, and so is warmth:
grade what the comment NAMES, never how it sounds, because a greeting, an
exclamation mark or a sentence about how annoying the bug is costs nothing
where some other clause is specific. Three failures, each quotable: text
that would read identically under any issue anywhere; a guarantee of a fix,
a deadline, or a demand for exclusivity; and a comment advertising proof
the attached report does not contain, which is worse than boilerplate
because it commits the contributor publicly to a repro they do not have.

**What good looks like, for the repo's conventions.** Most repos state no
AI policy, and silence imposes nothing. Among those that do, read the
sentence and ask exactly one question: does it require that AI use be
STATED, on the surface this package occupies — words posted as a comment on
an issue? Note the stage first, because most AI clauses you will meet bind
somewhere else. A duty to understand, test, review or be able to explain
your work is a comprehension duty, and nothing in a comment satisfies or
violates it. A ban on fully AI-generated contributions that leaves
assistive use allowed is not a disclosure rule. A disclosure duty written
for pull requests does not reach an issue comment, especially where the
same policy says in so many words that issue comments carry no disclosure
ask. A requirement that comments be written by a human in their own words,
with a warning that AI-generated ones may be hidden, is an authorship rule:
no sentence in any package can prove or disprove who typed it, disclosure
would not cure it, and it is never graded here — raise it in the summary as
something the contributor must satisfy themselves. CLAs, DCOs, vouch flows,
duplicate-search checkboxes and template fields bind at PR time or at
issue-opening time, not on a comment.

What DOES fail is the narrow case, and it is worth quoting in full because
it is the shape you are looking for: a policy stating that all AI usage in
any form must be disclosed, stating the tool used and the extent of the
assistance, and extending in its own words to AI-assisted issues and
comments — with no disclosure sentence anywhere in either draft. Compliance
is cheap and looks like one sentence: "Per the AI usage policy: I used an
AI assistant to help me organize this report; I ran and verified every step
myself and I understand what I'm reporting." Two traps at the edges. The
premise at the top of this guide means you never pass that row by reasoning
that perhaps no AI was used. And an AI tool's NAME appearing as the SUBJECT
of the bug — the workload that triggers the hang, the CLI under test — is
subject matter, never a disclosure and never a violation.
