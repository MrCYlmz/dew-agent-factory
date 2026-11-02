# Agent Factory

A [Claude Code](https://claude.com/claude-code) tool that runs software projects as a team of agents.

> **Status:** early draft. The structure and workflow are in place; most rule sets are first drafts and still being refined.

## How it works

You are the product owner. Three kinds of agents do the rest:

- **Architect** — sets up or onboards a project, interviews you until the architecture and conventions are clear, picks the starting team and owns every rule set. It is the only agent that changes rules, and only with your approval.
- **Team lead** — runs stories in the project folder: refinement, requirements, plan, build, PR, retro. It never writes code or rules and never tells a worker how to do its job; it hands out briefs and checks results.
- **Workers** — developers, testers, reviewers and other roles, each following its own rule set from the library plus a stack skill such as `java-spring` or `vue3`.

Every story passes through gates you approve: the requirements, the merge, and, when needed, domain docs and API changes or a UI draft. Unit testers write tests first, developers code until they pass, and reviewers check the PR. After each story, workers propose changes to their own rule sets in a retro. The architect reviews the proposals and applies the ones you approve, so the team improves with every story.

## Usage

Requires [Claude Code](https://claude.com/claude-code).

Set up a new project, or onboard an existing one, from this folder:

```bash
claude --agent architect
```

For an existing project, give the architect the paths of its repos. It reads the code first and asks only what the code cannot tell.

Run stories from the project folder it creates. Its `CLAUDE.md` makes that session the team lead:

```bash
cd <project folder> && claude
```

Fill or improve a rule set from this folder, for example:

```
/write-rule-set backend-reviewer
```

## Use with care

The agents write code, run commands, create branches and open pull requests in the repos you point them to. Review everything they produce before you merge it, and try the tool on a non-critical repo or a copy first. It is provided as is, without warranty; see the [license](LICENSE).

## Layout

```
.claude/agents/architect.md     the technical architect
.claude/skills/project-grill/   its interview with you
.claude/skills/write-rule-set/  how rule sets and stack skills are researched and written
library/common-rules/           rules every worker shares
library/rule-sets/              one skill per role
library/stacks/                 language and framework skills, such as java-spring
templates/project/              files for each new project, including CLAUDE.md for the team lead
templates/task/                 files for each story's task folder
templates/rule-set.md           pattern for filling a rule set
templates/layer-rules.md        pattern for a repo's layer rules
templates/agent.md              pattern for a worker's agent file
```

## License

[MIT](LICENSE)
