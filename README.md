# Agent Factory

A Claude Code tool that runs software projects as a team of agents. The concept is in [Agent Factory Tool – Concept](https://claude.ai/artifact/FEx2zNTREqcRGQE5RsVtxY).

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
