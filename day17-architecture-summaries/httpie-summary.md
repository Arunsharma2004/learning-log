# WHAT THE TOOL IS

HTTPie is a CLI-based tool used to send HTTP requests to APIs/web servers.
With this tool, you don't need a browser to interact with APIs or web
servers — you can send real HTTP requests directly from the terminal and
see the response, formatted and colorized, right there. Think of it as a
faster, friendlier alternative to tools like `curl` for testing HTTP
requests.

## HTTPie Request–Response Flow

This is HTTPie's own internal flow — how it processes YOUR command and
gets a response back, regardless of what kind of server you're talking to
(HTTPie has no knowledge of whether the server is built with FastAPI,
Flask, or anything else — that's entirely up to whoever runs the server):

Terminal command → cli/ (parses your flags and arguments, using the
Environment object throughout) → client.py (builds the actual HTTP
request and sends it via the `requests` library) → [travels across the
internet to whatever server you pointed it at] → response comes back →
output/ (decides how to render it: raw, encoded, or pretty/colorized,
again based on Environment) → printed to the terminal

## Environment Object Pattern

This is a single object that bundles together things like stdin, stdout,
stderr, and terminal state (e.g. is this a real terminal, or is output
being piped somewhere else), and gets passed explicitly into almost every
function throughout the pipeline, instead of those functions reaching for
Python's built-in `sys.stdout` directly.

Think of it as:
                     HTTPie
                     ↓
                     creates an Environment
                     ↓
                     Environment carries the correct stdout/stderr/terminal state
                     ↓
                     every function receives Environment as a parameter


This is conceptually similar to `Depends(get_db)` in my own
expense-tracker project — instead of every function reaching for a
global resource directly, it receives what it needs as a parameter. This
also makes the whole pipeline genuinely testable, since tests can build a
fake Environment (simulating a pipe, a non-terminal, etc.) without
needing a real terminal.

## Plugin System

A plugin is an additional piece of code that adds a specific capability
to HTTPie without needing to be built into its core.

If no authentication plugin is specified, HTTPie defaults to Basic auth
(username and password). Another built-in option is Digest auth, which
uses a challenge-response mechanism so the password itself is never sent
as plain text — a real security improvement over Basic.

I tested both the success and failure paths of Basic auth directly:
authenticating with the correct credentials against httpbin.org returned
a 200 with a JSON body confirming `"authenticated": true`; using a wrong
password returned a 401 with a `WWW-Authenticate` header and no body.

## A Note on Verification

While investigating the auth flow, Claude Code cited a specific function
(`_process_auth` in `argparser.py`, around line 282) as where auth
resolution happens. When I first checked, I searched the wrong file
(`client.py`) due to text that got truncated during copy/paste, found
nothing, and initially suspected the claim was inaccurate. After
re-checking against the correct file, the function was exactly where
originally claimed — the error was in how the truncated text got
interpreted, not in Claude Code's original claim. This was a good
reminder that verification has to target the *right* thing, since a
wrong conclusion can come from misreading a message, not just from an
AI error.