---
name: common-rules
description: Rules every worker follows.
---

## Scope

- Deliver what the story and `plan.md` ask, at the scope intended. If a better approach exists, say so in one sentence and still do what was asked, because unapproved extras widen every review.
- Add a dependency only when `plan.md` or the playbook names it, in the story whose code first imports it, and remove one you stop using. Each one is code the team must update and trust.
- When the requirements, the API spec or your rules leave a case open or contradict each other, ask the team lead and name the gap, instead of guessing or picking one. A guess becomes a decision nobody approved.

## Shared machine

- Write only inside your scope, even for a moment and restored afterwards: workers share the worktree and run lint and tests at the same time. Probes, scripts and scratch data go in your scratchpad directory and import worktree modules by absolute path.
- Never stop a process you did not start, such as a server already on the app's port: it may be the user's or another worker's run. Give your own runs a free port and a data directory in your scratchpad, never the user's, or report the conflict, and stop every process you started before you report.

## Code

- Read before you use: never assume a component's props, an API field or a file's contents without opening it.
- Write code that reads like the code around it: same naming, comment density and idioms. A reader should not be able to tell which lines an agent wrote. Where the code around it breaks your rule set, the rule set wins for the lines you write, and code the story does not touch stays as it is.
- Name things by what they are in the domain, using the words from the requirements and domain docs. Generic names like `data`, `item`, `handle` or `utils` force every reader to open the code to learn what it holds.
- Comment only what the code cannot say: a reason, a constraint, a workaround, about the lines the comment sits on. Never narrate what the code does or what you changed; that belongs in the commit message. Never claim anything about code outside those lines, such as "only called from X" or "no route does Y": nobody keeps such a claim true.
- Handle errors only where they can happen: at user input and external calls. Guards for impossible cases hide real bugs and bury the logic.
- Build no helper, abstraction or option for a single use or an imagined future need. The right amount of structure is what the current story needs.
- Solve the problem in general. Never shape code to the test inputs or special-case a test to make it pass; if a test looks wrong, say so.
- Leave no debug output, commented-out code or mock data behind. They reach production because nobody owns them.
