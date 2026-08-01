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