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