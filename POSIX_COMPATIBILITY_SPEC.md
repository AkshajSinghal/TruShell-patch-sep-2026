# POSIX compatibility spec for TruShell v3

Please read this first: this file is a small, specific spec. It is not
a claim that TruShell is POSIX-compliant. It isn't. POSIX defines a whole
Shell Command Language (lists, compound commands, redirections,
parameter expansion, the special builtins, and more) and we implement
a slice of it. This document says which slice, and what we check.

Our aim is that a script written for `/bin/sh` runs under TruShell, and
that if it doesn't, we fall back to something that can run it. That is
where we're going. This file describes where we are.

<!-- NOTE: the four criteria below are unchanged from the
     previous version. The rest of this file is new. Check the "How we
     test it" section against the actual acceptance tests before merging. -->


## Scope

The v4 shell is expected to support:

- an interactive prompt using standard input and output
- basic builtins such as `cd` and `exit`
- running external commands found through the host's PATH
- keeping quoted arguments intact when passing them to external commands
- falling back to running a line as an external command when it is not
  a TruShell expression (see "Known problems" below)


## Acceptance criteria

### 1. `cd` without arguments

- Running `cd` changes the working directory to the user's home
  directory.
- A following `pwd` prints that directory.

### 2. Quoted arguments for external commands

- `printf '%s %s\n' hello world` gives the program the arguments
  `hello` and `world`, exactly as written.
- The shell keeps whatever quoting the external process needs to see
  the intended arguments.

### 3. Builtin exit behaviour

- `exit` ends the interactive shell cleanly.
- The shell does not hang waiting for more input after `exit`.

### 4. Fallback to external commands

- If a line can't be parsed as a TruShell expression, the shell tries to
  run it as an external command.
- That's how ordinary POSIX-style commands keep working at the prompt.


## What this spec does not cover

Not covered here. Some may work, some don't, and we haven't promised any
of them:

- compound commands (`if`, `for`, `while`, `case`), functions
- command lists (`&&`, `||`, `;`) and subshell grouping
- parameter expansion (`${var:-default}` and friends), arithmetic
  expansion, command substitution
- globbing and tilde expansion rules
- here-documents
- the POSIX special builtins beyond `cd` and `exit`
- exit status rules, `set -e`, `set -u`
- signal and job-control behaviour as POSIX defines it
- bash extensions of any kind (`[[ ]]`, arrays, process substitution)

A full compatibility matrix, one row per feature marked supported,
partial, planned or won't-do, is on the to-do list. See TS-002 in
[`trushell-issues.md`](trushell-issues.md).


## How we test it

Acceptance tests for the four criteria live with the rest of the tests
in `tests/`. Run them with `cargo test`. They only prove what's written
above. Passing them does not mean your favourite script will run.

If a script that should work under `/bin/sh` doesn't, that's a bug or a
gap in this spec. Please open an issue with the script (or the smallest
piece of it that fails).


## Known problems

The fallback in criterion 4 is convenient and dangerous. A line that
fails to parse is run as an external command, so a typo can turn into a
PATH lookup, and what a line means depends on what happens to be
installed. It's also the sort of code path where injection bugs live,
and we've already had one. We plan to remove it or make it explicit
(TS-005 in [`trushell-issues.md`](trushell-issues.md)). When that
happens, criterion 4 will change and this file will say so.
