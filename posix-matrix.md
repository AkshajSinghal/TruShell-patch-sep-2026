# POSIX compatibility matrix

This is the honest list of what TruShell does and doesn't do compared
with the POSIX shell language. One row per feature. Each row has
something you can type and what should happen, so anyone can check it.

Last full run: not yet. Every row below starts as `unverified`, because
nobody has run them all against a real build. That's the next job.

Checked against commit: `<fill in>`
Checked by: `<fill in>`
Compared with: `<dash or another mainstream sh, fill in version>`

The short spec is [POSIX_COMPATIBILITY_SPEC.md](POSIX_COMPATIBILITY_SPEC.md).
It covers four behaviours. This matrix covers the rest.


## Status values

- `supported`: works as POSIX says. Checked, with a date and commit above.
- `partial`: works for the common case and fails for some. Say which in the notes.
- `planned`: not there yet, and we intend to build it. Link an issue.
- `wontfix`: we deliberately differ or don't do it. Say why.
- `unverified`: nobody has checked. The starting point.
- `undecided`: used only for non-POSIX extensions. See `docs/design/goals.md`.

Section numbers follow POSIX.1-2017 (Shell Command Language, chapter 2).
Later editions may number things differently.


## How to fill this in

1. Build TruShell from the commit you're checking.
2. Run each snippet in TruShell and, for comparison, in `dash` (or another `sh`).
3. If TruShell matches `dash`, mark `supported`. If part matches, mark
   `partial` and write what fails. If it fails, mark `planned` (with an
   issue number) or `wontfix` (with the reason).
4. Update the commit and date at the top.
5. Change this file in the same PR as any fix that changes a row.

Some snippets need a scratch file. They use `/tmp`. Clean up after yourself.


## Running the shell

| Feature | Try | Should happen | Status |
|---|---|---|---|
| Interactive prompt reads standard input | start `trushell`, type `echo hi` | `hi` | unverified |
| Run a script file | `trushell script.sh` (containing `echo hi`) | `hi` | unverified |
| Command string | `trushell -c 'echo hi'` | `hi` | unverified |
| Script on standard input | `echo 'echo hi' \| trushell` | `hi` | unverified |
| Unknown line runs as an external command (criterion 4) | `uname` | prints the OS name | unverified |


## Quoting and comments (2.2, 2.3)

| Feature | Try | Should happen | Status |
|---|---|---|---|
| Backslash escape | `echo a\ b` | `a b` | unverified |
| Single quotes | `echo 'a $b'` | `a $b` | unverified |
| Double quotes keep spaces | `echo "a  b"` | `a  b` (two spaces) | unverified |
| Quoted arguments arrive intact (criterion 2) | `printf '%s\|%s\n' 'a b' c` | `a b\|c` | unverified |
| Line continuation | `echo a\` then `b` on the next line | `ab` | unverified |
| Comments | `echo hi # nope` | `hi` | unverified |


## Variables and parameters (2.5)

TruShell's own `let x = 1` is not POSIX and doesn't count here.

| Feature | Try | Should happen | Status |
|---|---|---|---|
| Assignment | `x=hello; echo $x` | `hello` | unverified |
| Exit status parameter | `false; echo $?` | `1` | unverified |
| Positional parameters | `set -- a b; echo $2` | `b` | unverified |
| Argument count | `set -- a b; echo $#` | `2` | unverified |
| All arguments, quoted | `set -- a b; printf '%s\n' "$@"` | `a` then `b` on separate lines | unverified |
| Shell process ID | `echo $$` | a number | unverified |
| Environment is inherited | `echo $HOME` | your home directory | unverified |
| Export to children | `export X=1; sh -c 'echo $X'` | `1` | unverified |
| Assignment before a command | `X=1 sh -c 'echo $X'; echo ${X:-unset}` | `1` then `unset` | unverified |


## Word expansions (2.6)

| Feature | Try | Should happen | Status |
|---|---|---|---|
| Tilde | `echo ~` | your home directory | unverified |
| Default value | `echo ${nope:-fallback}` | `fallback` | unverified |
| Length and trimming | `x=hello; echo ${#x} ${x%lo}` | `5 hel` | unverified |
| Command substitution | `echo $(echo hi)` | `hi` | unverified |
| Arithmetic expansion | `echo $((1+2))` | `3` | unverified |
| Field splitting | `x="a b"; printf '%s\n' $x` | `a` then `b` on separate lines | unverified |
| Pathname expansion | `echo /etc/host*` | the matching names | unverified |
| Glob with no match | `echo /nope/*.zzz` | the literal pattern | unverified |

Notes:

- Field splitting: TS-006 proposes not splitting after expansion on
  purpose. If we go ahead, this row becomes `wontfix`, with the reason.
- Glob with no match: TS-013 leans towards making it an error. Same
  treatment if we decide that.


## Redirection (2.7)

| Feature | Try | Should happen | Status |
|---|---|---|---|
| Output to file | `echo hi > /tmp/t; cat /tmp/t` | `hi` | unverified |
| Append | `echo a > /tmp/t; echo b >> /tmp/t; cat /tmp/t` | `a` then `b` | unverified |
| Input from file | `cat < /tmp/t` | the file's contents | unverified |
| Error output to file | `ls /nope 2> /tmp/e; cat /tmp/e` | the error message | unverified |
| Merge error into output | `ls /nope 2>&1 \| cat` | the error message, through the pipe | unverified |
| Here-document | `cat <<EOF`, then `hi`, then `EOF` | `hi` | unverified |

Not tested yet: `>|`, `<>`, `<&`, `>&` with other descriptors.


## Exit status (2.8)

| Feature | Try | Should happen | Status |
|---|---|---|---|
| Success | `true; echo $?` | `0` | unverified |
| Command not found | `nosuchcmd; echo $?` | error message, then `127` | unverified |
| Found but not executable | `touch /tmp/x; /tmp/x; echo $?` | `126` | unverified |
| Killed by a signal | `sh -c 'kill -9 $$'; echo $?` | `137` | unverified |


## Commands and lists (2.9)

| Feature | Try | Should happen | Status |
|---|---|---|---|
| Simple command | `ls /` | a listing | unverified |
| Pipeline | `echo hi \| tr a-z A-Z` | `HI` | unverified |
| Negation | `! false; echo $?` | `0` | unverified |
| Sequential list | `echo a; echo b` | `a` then `b` | unverified |
| AND list | `true && echo yes` | `yes` | unverified |
| OR list | `false \|\| echo yes` | `yes` | unverified |
| Background job | `sleep 1 & wait; echo done` | `done` after about a second | unverified |
| Subshell | `(cd /tmp; pwd); pwd` | `/tmp`, then the original directory | unverified |
| Brace group | `{ echo a; echo b; } \| cat` | `a` then `b` | unverified |
| `if` | `if true; then echo y; else echo n; fi` | `y` | unverified |
| `for` | `for i in 1 2 3; do echo $i; done` | `1`, `2`, `3` on separate lines | unverified |
| `while` | `i=0; while [ $i -lt 2 ]; do echo $i; i=$((i+1)); done` | `0` then `1` | unverified |
| `case` | `case abc in a*) echo match;; esac` | `match` | unverified |
| Function | `f() { echo hi; }; f` | `hi` | unverified |


## Special built-ins (2.14)

| Feature | Try | Should happen | Status |
|---|---|---|---|
| `:` | `: ; echo $?` | `0` | unverified |
| `.` (dot) | `echo 'echo sourced' > /tmp/s; . /tmp/s` | `sourced` | unverified |
| `eval` | `eval 'echo hi'` | `hi` | unverified |
| `exec` | `exec echo hi` | `hi`, and the shell ends | unverified |
| `readonly` | `readonly r=1; r=2` | an error, non-zero status | unverified |
| `set -e` | `set -e; false; echo no` | shell exits, `no` is not printed | unverified |
| `shift` | `set -- a b; shift; echo $1` | `b` | unverified |
| `trap` | `trap 'echo bye' EXIT; exit` | `bye` | unverified |
| `unset` | `x=1; unset x; echo ${x:-gone}` | `gone` | unverified |
| `break` | `for i in 1 2 3; do [ $i = 2 ] && break; echo $i; done` | `1` | unverified |
| `return` | `f() { return 3; }; f; echo $?` | `3` | unverified |


## Other built-ins and utilities

| Feature | Try | Should happen | Status |
|---|---|---|---|
| `cd` with no argument (criterion 1) | `cd; pwd` | your home directory | unverified |
| `cd -` | `cd /tmp; cd /; cd -` | `/tmp` | unverified |
| `pwd` | `cd /tmp; pwd` | `/tmp` | unverified |
| `exit` (criterion 3) | `exit` | shell ends, doesn't hang | unverified |
| `exit` with a status | `trushell -c 'exit 3'; echo $?` | `3` | unverified |
| `read` | `echo hi \| { read x; echo $x; }` | `hi` | unverified |
| `test` and `[` | `[ 1 -lt 2 ] && echo yes` | `yes` | unverified |
| `command -v` | `command -v cd` | `cd` | unverified |
| `alias` | `alias hi='echo hello'; hi` | `hello` | unverified |
| `umask` | `umask` | the current mask | unverified |
| `kill` and `wait` | `sleep 30 & kill $!; wait $!; echo $?` | `143` | unverified |


## Job control

Optional in POSIX `sh`, expected in an interactive shell. Merged in v3.

| Feature | Try | Should happen | Status |
|---|---|---|---|
| Ctrl-C stops only the running program | `sleep 30`, press Ctrl-C | `sleep` stops, the shell stays | unverified |
| Suspend and resume | `sleep 30`, Ctrl-Z, then `fg` | stops, then resumes | unverified |
| Job list | `sleep 30 &` then `jobs` | the job is listed | unverified |


## Not POSIX (extensions from bash and friends)

These aren't in the POSIX shell language. They're listed so nobody has to
guess. See [`docs/design/goals.md`](../design/goals.md). The plan for
bash-only features isn't decided.

| Feature | Try | Status |
|---|---|---|
| `[[ ... ]]` | `[[ a == a ]] && echo yes` | undecided |
| Arrays | `a=(1 2 3); echo ${a[1]}` | undecided |
| Process substitution | `cat <(echo hi)` | undecided |
| `source` | `source /tmp/s` | undecided |
| Brace expansion | `echo {1..3}` | undecided |
| Here-strings | `cat <<< hi` | undecided |
| `&>` redirect | `ls /nope &> /tmp/e` | undecided |


## Not covered yet

Things this matrix doesn't test at all: `getopts`, `times`, `ulimit`,
signal edge cases (`trap` for signals other than EXIT), locale handling,
parameter expansion forms beyond the ones above, and the exact rules for
what happens after a shell syntax error. If a script hits one of these,
please open an issue.
