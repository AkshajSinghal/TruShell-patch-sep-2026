# How TruShell is run

This describes how things work today. It isn't a promise about how they
will always work. TruShell is a small project and we'd rather write down
what's true than invent ceremony we don't use. When it stops being true,
we'll change this file.


## Who decides

TruShell is maintained by TruFoundation.

Maintainers review and merge pull requests, cut releases, and decide what
goes in and what doesn't. Anyone else is a contributor. Being a
contributor is not a lesser role. Most of the work, including a good part
of the v3 sprint, came from contributors.


## How decisions get made

- Most things are decided in pull requests and issues, in public.
- Small changes: one maintainer's approval is enough.
- Changes that alter what existing input means, the permission model,
  the language, or the plugin API: open an issue or discussion first.
  We'd like at least a few days of comment before merging, and more
  than one person to have looked.
- When we disagree we talk it through. If we can't agree, the
  maintainers decide and write down why. The reasoning matters more than
  the outcome, and anyone can point out that it's wrong.
- Goals and non-goals live in
  [`docs/design/goals.md`](docs/design/goals.md) and are summarised in
  the README. Changing them is a maintainer decision, made in the open.


## Sprints and claims

For bigger pushes (v3 was one) we've used a discussion thread with a
checklist. Contributors claim a task by commenting with what they're
taking and a target date, and the maintainers open an issue for each
task. This works well enough that we'll probably keep doing it.


## Becoming a maintainer

We haven't formalised this. In practice it's people who keep showing up,
ship careful patches, and review other people's work. If you're
interested, say so in a discussion. Nobody will think less of you for
asking.


## Releases

Maintainers tag releases. Release notes list what changed and who did it.
Only `main` and the latest tag get fixes.


## Conduct and security

- Conduct: [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md)
- Security reports: [`SECURITY.md`](SECURITY.md)

