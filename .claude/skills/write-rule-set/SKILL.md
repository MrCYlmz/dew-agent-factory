---
name: write-rule-set
description: Write or revise a rule set, stack skill or common-rules in the library, based on research into how the current Claude model fails at that work. Use when asked to fill, write, improve or trim one.
argument-hint: <rule set or stack name>
---

Write or revise `$ARGUMENTS` so it fixes what the current Claude model actually gets wrong in that role, and nothing it already does well.

## 1. Read what exists

Read the target file, `templates/rule-set.md`, `library/common-rules`, the rule sets of roles that work next to this one, and the stack skills those roles preload. You need them to avoid repeating a rule that already lives elsewhere.

## 2. Settle open choices

If the rules depend on a choice nobody has made yet, such as a framework, version or tool, ask the user before researching. Ask one question at a time and give your recommendation.

## 3. Research

Open every page you take a fact from; a search snippet is not a source.

- Anthropic's prompting guidance: the general best practices page and the page for the current model.
- Studies and practitioner reports of how LLMs fail at this kind of work, preferring recent ones and the current model.
- The official docs and standards for the domain, at current versions.

## 4. Decide where each finding goes

- The current model already does it well by default: leave it out.
- A well-known standard the model already knows: one line naming it, without a link, because a link invites fetching a page the model does not need.
- A tool can check it: a linter or check under "Done when", not prose.
- Every role needs it: `common-rules`. Architecture: layer rules. A language or framework: the stack skill. Test tools: the stack skill's Testing section.
- Otherwise: a rule in this rule set.
- A judgment call rather than a rule: "Good practice", only if needed.

## 5. Write

- Rule sets follow the seven sections of `templates/rule-set.md`. Stack skills use whatever sections fit.
- Give each rule its reason in a few words, and write plainly, without capitals for emphasis.
- Where the model reaches for outdated APIs, name the current one and the old one it replaces.
- Leave out instructions to think carefully, double-check or verify. Current models check their own work, and such instructions only add cost.

## 6. Check

- No rule appears twice across the files one agent preloads: `common-rules`, its rule set, its layer rules and its stack skill.
- Those files stay under about 2,000 tokens together, roughly 1,450 words. If they don't, cut the rules that fix the least, or move a rule to the file where it belongs.
- When revising after a retro, also remove rules whose failure no longer shows up.

## 7. Report

Give the user a table of each problem found, the rule that answers it, and its source link. Links belong in the report, not in the skill. Then list what you left out and why, the sizes, and any open questions.
