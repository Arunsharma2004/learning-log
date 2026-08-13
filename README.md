# Learning Log — Claude Code Mastery

## Day 1 :

Set up dev environment: Git, Node.js, Python, Windows Terminal.
Learned core terminal commands (cd, ls, mkdir, cat, cp, mv, pipes, redirects).
Set up SSH keys and connected to GitHub.
Created GitHub + Claude accounts.

## Day 2 :

Learned Git branching, merging, and conflict resolution hands-on.
Made 9 real commits, created and merged a feature branch, and deliberately
triggered + resolved a merge conflict (including a tricky binary-encoding
issue with PowerShell's `>` redirect).

Simulated a two-person workflow using two local clones (dev-a, dev-b):
pushed from one, got a "rejected — fetch first" error on the other,
fixed it with `git pull`, then pushed and verified both sides ended up
with each other's work.

**Key lesson:** switching branches (`git checkout`) physically changes
the files in your folder to match that branch's snapshot — it's not
just a label, it's a real state change.

## Day 3

Refreshed core Python: variables, functions, conditionals, loops,
lists, dictionaries, tuples, file I/O (with open), and error
handling (try/except).

Wrote 5 scripts by hand (no AI): word counter, calculator, CSV
parser, file renamer, JSON reader.

## Day 4

Learned modules, virtual environments (venv, pip freeze), classes
(constructors, self, methods, state mutation), and @staticmethod
decorators.

Built a full CLI todo app by hand: task.py (Task class), storage.py
(save/load JSON), main.py (argparse CLI with add/list/done commands).
Wrote 5 pytest tests covering creation, mark_done, to_dict, from_dict,
and a full round-trip conversion test — all passing.

## Day 5

Installed Claude Code CLI and logged in with Claude Pro. Ran my first
real Claude Code session inside todo-app.

Asked Claude Code to explain the project and review main.py - it
correctly identified real bugs: done command crashed on invalid/
out-of-range input, no delete command existed.

Fixed the done command's error handling (try/except/else), then
directed Claude Code to add 3 features: better unknown-command
message, a delete command, and an edit command. Reviewed every diff
before approving - traced through Python's reference semantics
(deleting from a list doesn't destroy the object a variable already
points to) and confirmed why capturing old values before overwriting
matters for edit's confirmation message.

## Day 6

Learned how Claude Code's context window works - every prompt, file
read, and tool output gets resent every turn, and quality degrades
as a session gets long and cluttered with unrelated tasks.

Ran a real comparison: asked Claude Code to add a priority field to
Task after 4 unrelated tangents in one session - got back genuinely
broken syntax (mismatched quotes/parens). Same exact request in a
fresh session (after /clear) produced clean, correct code with a
smart backward-compatibility fallback (.get() instead of direct key
access).

Practiced /clear (full reset) and /compact (summarize, keep working).
Learned the habit of writing key decisions into code comments/files
instead of leaving them only in chat, since conversations can be
cleared or degrade but files persist. Also learned: after 2 failed
correction attempts, clear and restate fresh rather than piling on
more corrections in the same confused session.

Shipped a real feature (task priority: high/medium/low) to the todo
app, fully tested and committed.

## Day 7

Week 1 consolidation. Rebuilt the todo app from scratch by directing
Claude Code from a plain-English spec instead of writing every line
myself - reviewed a genuinely different architecture (argparse
subparsers, per-command functions, @classmethod, __repr__), verified
new concepts hands-on (tested .pop() vs del, tested JSON corruption
handling) rather than trusting explanations. Wrote a README with
Claude Code's help.

Wrote and answered 14 self-quiz questions covering Git, terminal,
Python, JSON, pytest, and how to review AI code without deep
programming expertise - including honest discussion on context
windows, token costs, and the AI-and-jobs question.

**End of Week 1.** Foundation built: terminal fluency, Git branching/
merging/conflicts, Python fundamentals, and the core discipline of
reviewing AI-generated code rather than approving it blindly.

## Day 8

Learned what CLAUDE.md is - a persistent file Claude Code reads at
the start of every session, giving it project context without
re-explaining everything each time. Ran /init on todo-app, which
generated an accurate CLAUDE.md documenting real architectural
decisions (like why PRIORITIES is a tuple, and the consistent
try/except/else pattern across done/delete/edit).

Ran the bad-vs-good CLAUDE.md exercise: swapped in a vague, generic
CLAUDE.md and asked Claude Code to add a clear command, then asked
about extending the pattern to a hypothetical snooze command. Result
was surprising - Claude Code still correctly inferred the established
patterns by reading the actual code directly, even without CLAUDE.md
spelling it out. It even independently spotted the same code-
duplication concern the good CLAUDE.md documented.

**Honest takeaway:** CLAUDE.md's value didn't show up dramatically on
this small, consistent codebase, since Claude Code compensated by
reading the code itself. The real benefit should become clearer on
larger, more complex projects in Week 2+ - worth keeping the habit
even though today's test didn't show a dramatic difference.

## Day 9

Learned Claude Code's memory systems. Explored /memory - three types:
project memory (CLAUDE.md, version-controlled), user memory (global,
~/.claude/CLAUDE.md), and auto-memory (Claude Code's own notes,
stored locally outside the project).

Told Claude Code to remember venv activation before pytest, then
verified a brand-new session correctly recalled it - proving auto-
memory persists independently of conversation/context history.
Inspected the actual file (feedback_venv_pytest.md) and found it
wasn't a raw copy of my instruction - Claude Code expanded it with
reasoning (why) and broader application (single-test runs too).

Reasoned through why auto-memory alone wasn't enough for this fact
(machine-local, doesn't travel with git clone) and added the same
info to CLAUDE.md so it's available to anyone working on the
project, not just this machine.

## Day 10

Learned Plan Mode - Claude Code proposes a plan before touching any
files, letting me review the approach itself, not just the resulting
diff. Gave it a real refactor task: collapse the duplicated try/except
pattern across done/delete/edit into a shared resolve_task helper.

Pushed back twice before approving:
1. Caught that the first version returned an inconsistent type - a
   tuple on success, implicit None on failure - which would crash
   with a TypeError if unpacked without a truthiness check first.
   Verified this hands-on in a Python shell, got it fixed to always
   return a consistent 2-tuple.
2. Requested an explicit verification step for index 0 specifically,
   since 0 is falsy in Python - confirmed the fix correctly uses
   "is not None" rather than bare truthiness, so index 0 isn't
   mistaken for failure.

Also learned CLAUDE.md and code can genuinely conflict - Claude Code
explicitly flagged that my refactor request contradicted CLAUDE.md's
documented "don't use a shared helper" guidance, and asked how to
proceed before drafting a plan.

Real-world lesson: a laptop shutdown mid-review lost my in-progress
critique (conversation-only, not yet in any file) - the next session
regenerated the same original buggy version, and I had to re-catch
and re-fix the same bug. Direct proof of why decisions worth keeping
belong in files, not just conversation.

Also hit a minor real mishap: verification commands overwrote
tasks.json with test data; Claude Code's backup attempt silently
failed. Restored via `git checkout -- tasks.json` - original data
turned out to be safe since it matched the last commit. Good proof
of why committing regularly matters.

## Day 11

Learned @-file references for precise file targeting in Claude Code.
Ran real vague-vs-specific comparisons: "make the todo app better"
triggered clarifying questions, while fully-specified prompts for
search, completed, pending, and priority commands each went straight
to correct implementations with zero back-and-forth once built.

Wrote 5 more prompt pairs beyond that. Two revealed a genuinely
important lesson beyond vague-vs-specific: even a well-specified
prompt can be technically infeasible - end-of-day notifications and
OS popups don't fit a plain CLI tool with no background process or
GUI, and a "total tasks in a day" / "defer to next day" pair both
secretly depended on a date/timestamp field that doesn't exist
anywhere in the Task class. Good prompting means checking feasibility
and data-model fit, not just clarity of wording.

Real incident: accidentally ran `clear` on real task data. Discovered
Git only had old task data committed - tasks.json wasn't being
tracked after every change. Decided to stop tracking it in Git
entirely (user data, not source code); hit a .gitignore merge-line
bug in the process (same category as Day 4's indentation bug). Found
and fixed a real test gap along the way - test_to_dict wasn't
checking the priority field, verified by deliberately reproducing
the AssertionError.

Second incident: Claude Code silently deleted tasks.json again during
its own test cleanup for the completed command. Pushed back
explicitly rather than accepting "nothing to fix" - it wrote a new
auto-memory file acknowledging the repeated pattern and committing to
ask first going forward. Verified the memory file was genuinely
written to disk.

Built the priority command last - correctly ranked priority using
PRIORITIES.index() instead of alphabetical string sort, and relied on
Python's sorted() stability guarantee to automatically preserve
insertion order for same-priority tasks, exactly matching the
tiebreak rule specified in the original prompt. Verified with a
prediction before running, confirmed correct.

Shipped 4 real features today: search, completed, pending, priority.

## Day 12 (Project 1, Part 1)

Started Project 1: Personal Expense Tracker with FastAPI. Wrote
CLAUDE.md BEFORE any code existed, based on real reasoning through
the data model - decided Expense needs id/amount/category/date,
correctly rejected "budget" as a separate concept, understood why a
dedicated unique id is necessary (category+date alone can still
collide on real data).

Used Plan Mode to review the full architecture before any code was
written. Learned the models.py vs schemas.py distinction - models.py
defines physical storage, schemas.py is the validation boundary
between untrusted user input and the database (e.g. rejecting
negative amounts or invalid categories before they're ever stored).
Understood SQLAlchemy's engine/session/Base pattern and FastAPI's
dependency injection (Depends(get_db)) conceptually before seeing
it work.

Ran the server for the first time (uvicorn), confirmed expenses.db
was created automatically via Base.metadata.create_all(). Explored
FastAPI's auto-generated /docs page and used it to genuinely test
every documented behavior: 201 on create, 204 on delete (learned the
200 vs 204 distinction the hard way - predicted 200, was wrong),
404 for a nonexistent id, and 422 for both an invalid category and
a negative amount - proving schemas.py's validation boundary
actually works, not just trusting the plan's description.

Set up .gitignore (venv/, __pycache__/, *.db - first time using a
wildcard pattern), pushed the first real backend project to GitHub.

First genuinely new category of project - a live server that stays
running and responds to requests, instead of a CLI tool that runs
once and exits.

## Day 13 (Project 1, Part 2)

Finished the Expense Tracker. Built a real pytest suite using
FastAPI's TestClient - learned to distinguish "shape checks" (does
an error response contain a `detail` key) from "content checks"
(exact field values), since a rejected request never actually creates
data to check values against. Hit and fixed a real bug in a test I
wrote myself - tried to DELETE with an id in a JSON body instead of
the URL path, corrected after tracing through how the route was
actually defined.

Resolved a genuine library deprecation warning (httpx -> httpx2 for
TestClient) - verified it was a real, current issue via search before
fixing it, rather than ignoring or blindly patching.

Built the monthly summary endpoint - genuinely new territory:
SQLAlchemy's group_by + func.sum() for real database aggregation,
FastAPI Query parameters, and reasoned through why group_by naturally
excludes categories with zero matching expenses (nothing to group)
rather than showing them as zero. Designed the response shape myself
first (category -> total, not individual line items) before writing
the prompt.

Wrote a README with Claude Code's help, verified accuracy against
everything actually built and tested (same discipline as Day 7).

Project 1 complete: full FastAPI + SQLite backend, validated inputs,
6 passing tests, aggregation endpoint, documented.