# Security

TruShell is alpha software. It has not had an outside security review,
and we assume it has bugs we haven't found. That said, a shell sits
close to everything a user owns, so we take reports seriously and
would rather hear about a problem early.


## Reporting a problem

Please do not open a public issue for a security bug.

Use GitHub's private reporting: go to the Security tab of the
repository and choose "Report a vulnerability". That reaches the
maintainers and nobody else.

<!-- VERIFY: turn on "Private vulnerability reporting" in the repository
     settings, or replace this section with a real contact address.
     Without one of the two, this file promises something we can't do. -->

Useful things to include:

- the commit or release you tested
- your OS and kernel (`uname -a`)
- the exact input, and what you expected to happen instead
- whether you need a plugin, a special config, or a certain terminal

A short reproducer is worth more than a long write-up.

We're a small team and we don't have a formal response time yet. We'll
answer as fast as we can, tell you if we agree it's a bug, and keep you
posted while we fix it. We'd like to fix first and talk publicly second,
and we're happy to credit you unless you'd rather we didn't.


## What counts

Things we want to hear about:

- A plugin getting out of the WASM sandbox, or using a capability it
  never declared.
- A way to make the shell run a command the user didn't type: injection
  through parsing, expansion, quoting, or the fallback path that hands
  unparsed lines to the system.
- Crashes, hangs or memory problems triggered by hostile input (a
  file name, an environment variable, pasted text, terminal escape
  sequences).
- Leaking terminal state or file descriptors into child processes in
  ways that matter.

Things that are not vulnerabilities, however unfortunate:

- "TruShell let me run `rm -rf`." A shell runs commands. It doesn't
  yet sandbox the commands you type, and we say so in the README.
- Problems that need you to already control the account, the machine
  or the config file.
- Bugs in programs TruShell starts, as opposed to how TruShell starts
  them.

If you're not sure which side of the line something is on, report it
anyway.


## Supported versions

Only `main` and the most recent tagged release get fixes. Older tags
are for the history books.


## What we don't promise

There is no bug bounty. There is no guarantee that the sandbox around
plugins is airtight; we would like it to be, and that's why we want the
reports. Please don't rely on TruShell to contain code you don't trust.
