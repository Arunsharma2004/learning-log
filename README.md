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

## Day 14

Started Week 2 debugging practice on the Expense Tracker. Introduced
five deliberate bugs and tested Claude Code's diagnosis without
giving it the answer each time:

1. A typo (get_db -> get_dbb) inside a Depends() default parameter -
   crashed immediately at server startup, not when the route was
   called, since default parameter values evaluate at import time.
   Correctly predicted this before running it. Python's own NameError
   included a "Did you mean: get_db?" suggestion - a built-in
   language feature, not something Claude Code added.

2. A subtle boundary bug (changed >= to > in a date filter) - no
   crash, no error, just a silently wrong result (empty summary for
   an expense dated exactly the 1st of the month). Gave Claude Code
   only the symptom, no error message, and it still correctly traced
   it to the exact comparison operator issue.

3. Changed amount from Float to Integer in models.py, expecting
   decimal amounts to be rejected. They weren't - the value passed
   through completely intact. Learned two layered reasons: schemas.py
   still declared amount as float (validation happens there,
   independent of the database model), AND SQLite uses "type
   affinity" rather than strict typing - a column declared INTEGER
   will still accept a decimal it can't cleanly convert, rather than
   rejecting it. SQLite-specific behavior; stricter databases like
   PostgreSQL would likely reject it outright.

4. Removed Depends(get_db) from POST /expenses entirely. Correctly
   predicted NameError. Discovered it crashed at REQUEST time, not
   startup - contrasted directly with bug #1's import-time crash.
   Learned the precise reason: default parameter values evaluate
   when Python reads the function definition; function body code
   only runs when the function is actually called.

5. Changed ExpenseOut's id field from int to str. Predicted Pydantic
   would just convert the integer to a string - wrong. Got a
   ResponseValidationError instead (500, not 422) - learned the
   distinction between request validation (ExpenseCreate, 422) and
   response validation (ExpenseOut, 500) as two separate jobs
   Pydantic does. Also learned 500 errors hide details from the
   client for security - the real traceback only shows in server
   logs, not the API response.

Real lesson from bug 2: a 200 OK response doesn't mean correct - it
just means no exception was thrown. Silent logic bugs are more
dangerous than crashes precisely because nothing signals something's
wrong.

Also learned a real Git lesson: after reverting bug 3 back to its
original state, `git status` showed "nothing to commit" - not
because nothing happened, but because the final file content matched
what was already committed (bugs 1 and 2's fixes had already been
swept into an earlier feature commit, coincidentally). Verified this
with `git diff HEAD` and `git log` before trusting it, rather than
assuming something was lost.

Updated CLAUDE.md with a "Lessons Learned" section covering all
three big discoveries: SQLite type affinity vs schemas.py as the
real enforcement layer, always checking server logs behind a 500,
and 200 not guaranteeing correctness.

**Week 2 retrospective:** CLAUDE.md and Plan Mode were the most
significant workflow shifts this week - not just concepts, but real
changes in how I approach giving Claude Code work. Confirmed my Day 8
CLAUDE.md experiment conclusion: value shows up through consistent
updates over time, not necessarily in a single side-by-side test on
a small project.

**Week 2 complete:** CLAUDE.md, memory systems, Plan Mode, prompting
discipline, Project 1 (Expense Tracker - FastAPI + SQLite, validated,
tested, documented), and 5 categories of real bugs diagnosed.

## Day 15

Learned real Git collaboration workflows, directed through Claude
Code instead of typing every command myself. Built a genuine feature
end-to-end on a branch: PUT /expenses/{id} update endpoint, using
Plan Mode to review the design (decided on a separate ExpenseUpdate
schema over reusing ExpenseCreate, for future flexibility). Verified
the update actually persisted with a fresh GET, not just trusted the
PUT response. All 9 tests passing.

Went through the full real workflow for the first time: branch,
build, test, commit, push, open a PR on GitHub, review the diff,
merge, delete the branch, pull the merge down locally. Learned gh
CLI wasn't installed - Claude Code was transparent about the
limitation and gave a working manual fallback instead of pretending.

Deliberately created a merge conflict (two branches, same line,
different comments). Caught a real discrepancy in Claude Code's own
explanations - it initially described "keeping both comments" but
its actual resolution dropped both, since they were placeholder text
with no functional value. Verified this directly against the file
rather than trusting either description. Real lesson: an AI's
narrated explanation and its actual action aren't guaranteed to
match - always verify against ground truth.

Deepened the "isolated snapshots" mental model for branches through
hands-on repetition - each branch holds its own complete copy of
file content, only reconciled at merge time.

## Day 16

Completed Test-Driven Development on the Expense Tracker - built a
full Budgets feature using the Red-Green-Refactor cycle, twice.

Feature 1 (budget creation): wrote test_create_budget before any
Budget model/schema/route existed. Watched it fail with a real 404.
Reviewed Claude Code's plan, made a real design decision - rejected
BudgetOut inheriting from BudgetCreate in favor of an independent
schema, to stay consistent with the existing Expense pattern rather
than introducing a second structural convention. Approved, verified
Green, zero regressions.

Feature 2 (budget spending check): wrote test_check_budget, which
needed real thought to get right - initially tried passing spent/
remaining as query parameters before realizing those are outputs to
calculate, not inputs to supply. Learned the difference between 404
(route doesn't exist) and 405 (route exists, wrong method) from the
first Red result.

Hit a genuinely valuable, self-discovered bug: the test failed with
spent=551, then 301, instead of the expected 250, because all tests
share the same database and aren't isolated from each other. Traced
it precisely - even test_update_expense_invalid_category, whose
whole point is testing that an invalid update gets REJECTED, still
leaves behind a real, valid groceries expense from its own setup
step, since the rejection only blocks the update, not the original
creation. That leftover data silently contributed to an unrelated
test's totals.

Made a deliberate engineering tradeoff rather than either ignoring
the problem or over-engineering mid-session: changed the brittle
== 250 assertion to >= 250 (proves the feature works without
requiring unrealistic full isolation), and documented the real fix
(a separate, isolated test database) as a "Known Limitations" entry
in CLAUDE.md, specifically so the workaround doesn't get silently
"corrected" back to == later by someone who doesn't know why it's
written that way.

All 11 tests passing. Real lesson: a test's own setup steps can have
side effects that leak into completely unrelated tests, even when
the test's core assertion (rejecting invalid input) is working
correctly.

## Day 17

Explored two real open-source Python projects with Claude Code:
httpie (CLI HTTP client) and full-stack-fastapi-template (FastAPI +
SQLModel).

Practiced verifying AI explanations of unfamiliar code rather than
trusting them outright - checked specific claims against real files
(entry points, class names, function locations). Traced one
discrepancy back to truncated text being misread, not an actual AI
error. Installed and ran httpie hands-on to test the plugin/auth
system for real, not just read about it.

In the FastAPI template, compared its unified SQLModel inheritance
pattern against my own project's independent schemas, and reasoned
through why both are valid choices depending on project maturity.
Found a genuinely useful pattern (SessionDep dependency aliases) and
documented it in my own project's CLAUDE.md. Also found a real
inconsistency - a crud.py function that's defined but never actually
called.

Biggest discovery: Claude Code flagged old-style exception syntax as
a bug. Disproved it through direct testing, then found the real
explanation - Python 3.14 made that exact syntax valid very recently
(PEP 758). Outdated AI knowledge, not a hallucination - a real lesson
in why verification matters even for confident, specific claims.

Wrote two 1-page architecture summaries in day17-architecture-
summaries/, reflecting only what was actually verified.

## Day 18

Set up ruff on the Expense Tracker. Found 7 warnings about Depends()
in default arguments (B008) - investigated whether this was a real
issue or a FastAPI-specific exception, confirmed it's the standard,
correct FastAPI pattern, and configured a documented ignore in
pyproject.toml rather than either blindly "fixing" working code or
silently dismissing the warning forever.

Added return type hints to all 7 routes. Worked through why this
isn't redundant with response_model - the type hint describes what
the Python function itself returns (the SQLAlchemy model), while
response_model governs the separate, runtime conversion FastAPI does
before sending the actual HTTP response. Two different checks, at
two different times, for two different audiences.

Extracted two genuine duplications (month_bounds for date-range
calculation, get_expense_or_404 for the lookup-or-404 pattern) -
same category as Day 10's resolve_task extraction. Correctly left a
third candidate (the add/commit/refresh pattern) unextracted, since
Claude Code's own reasoning was sound: too small and too standard to
benefit from a generic helper.

Documented a standing quality gate in CLAUDE.md - run ruff and pytest
after every future change, rather than relying on remembering to ask
each time.

Also caught a real, stale claim earlier this week: asked Claude Code
for a general health check, and it correctly flagged that CLAUDE.md's
Overview still said "not budgets" despite the feature existing since
Day 16. Verified the claim directly before fixing it.

Did a final naming review, closing the last gap in today's roadmap
goal. Fixed two real parameter-order inconsistencies (get_expense_or_404,
month_bounds) that could have caused a silent swap bug later. Declined
one suggestion (renaming BudgetStatus to BudgetStatusOut) after real
reasoning - the *Out suffix currently signals "mirrors a stored
database row," and BudgetStatus is a computed result, not a row echo;
forcing uniform naming would have erased that real distinction. Added
a comment explaining the exception instead of either blindly renaming
or leaving it unexplained.

## Day 19

Started Project 2: URL Shortener (FastAPI + SQLite). Reasoned through
the data model from scratch - Link(id, original_url, short_code,
click_count). Considered richer per-click timestamp tracking (a
separate Click table) but deliberately deferred it as a documented
future enhancement, keeping today's scope realistic. Wrote CLAUDE.md
before any code.

Used Plan Mode for the backend architecture. Worked through two
genuinely new concepts: why short_code uniqueness needs both a
Python-level pre-check AND a database unique constraint (a race
condition where two near-simultaneous requests could both pass the
check before either save completes - understood via a movie-seat
double-booking analogy), and why the redirect uses 307 instead of
301/308 (307 prevents browser caching, ensuring every visit actually
reaches the server and gets counted - a cached redirect would
silently undercount clicks).

Found a genuinely interesting real behavior while testing: /docs'
"Try it out" button showed a CORS/fetch error on the redirect route,
but the request still reached the server and incremented click_count
- confirmed by checking stats before/after. Real lesson: a request
failing from the client's perspective (couldn't process the response)
doesn't mean it failed on the server - the actual work still happened.

Verified all error cases: 404 for nonexistent short codes on both the
stats and redirect routes, 422 for an invalid URL on /shorten. Pushed
to GitHub (github.com/Arunsharma2004/url-shortener).

## Day 20

Built the URL shortener's frontend - a single HTML/JS file with a
form, copy-to-clipboard, and friendly inline error handling (no
alert() popups, no raw status codes shown to users). Had to add CORS
middleware to the backend, since index.html (opened via file://) and
the API (http://127.0.0.1:8000) count as different origins.

Traced the full request flow end-to-end from click to database and
back, and clarified a genuinely important distinction from Day 19's
CORS discovery: that issue was about a redirect response being
blocked; this one was about the initial request itself needing
permission to reach the server at all - same guard (CORS), two
different trigger points.

Verified all four frontend cases thoroughly: golden path (real short
URL, working copy button), empty input (caught client-side, zero
network calls - confirmed via the Network tab), invalid URL (real 422
translated into a friendly message), and server-down (network failure
caught with a generic fallback).

Implemented rate limiting on /shorten (5/minute per IP) using slowapi.
Reasoned through why only /shorten needed protection, not the redirect
route - /shorten creates unbounded new database rows, while the
redirect only increments click_count on an existing row, so it
doesn't carry the same growth risk. Documented this in CLAUDE.md
rather than applying rate limiting uniformly. Verified with real
testing: 5 requests succeeded, the 6th correctly returned 429.

Wrote a pytest suite using a fresh in-memory database per test - the
proper fix for the test isolation problem discovered on Day 16,
naturally available here since this is a new project. All 6 tests
passed on the first run.

Wrote and verified the README against the actual code and CLAUDE.md
reasoning, rather than trusting it blindly.

Installed Docker Desktop for the first time (needed WSL2 - CPU
virtualization was already on, but WSL2 itself wasn't installed).
Wrote a Dockerfile and .dockerignore, understood layer caching (why
requirements.txt is copied and installed before the rest of the code)
and why the Dockerfile/.dockerignore themselves are excluded from the
final image despite Docker needing to read them to build it.

Built and ran the actual container, verified all three routes worked
correctly inside it. Hit a confusing moment testing the redirect route
through /docs - misread a generic CORS error message as an
instruction to add http:// to the short_code field, which caused a
real, separate 404. Traced it back to the actual, expected Day 19
quirk (fetch() blocking cross-origin redirects) once tested correctly
via direct browser navigation.

Project 2 (URL Shortener) is now complete: backend, frontend, rate
limiting, tests, README, and a working, verified Dockerfile - all
pushed to GitHub.

## Day 21 