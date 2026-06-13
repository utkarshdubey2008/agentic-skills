# Skill Anatomy

This document describes the structure and format of skill files in this repository. Use it as a guide when contributing new skills or understanding existing ones.

## File Location

Every skill lives in its own directory under `skills/`:

```
skills/
  skill-name/
    SKILL.md           # Required: The skill definition
    examples.md        # Optional: Reference examples loaded on demand
```

`SKILL.md` is the only required file. Add `examples.md` only when annotated examples would help calibrate quality — and only when they would exceed 60–80 lines inline.

## SKILL.md Format

### Frontmatter (Required)

```yaml
---
name: skill-name-with-hyphens
description: What the skill does. Use when [specific trigger conditions].
---
```

**Rules:**
- `name`: Lowercase, hyphen-separated. Must match the directory name.
- `description`: Start with what the skill does in third person, then include one or more clear "Use when" trigger conditions. Maximum 1024 characters.

**Why this matters:** Agents discover skills by reading descriptions. The description must tell the agent both what the skill provides and when to activate it. Do not summarize the workflow — if the description contains process steps, the agent may follow the summary instead of reading the full skill.

### Standard Sections

```markdown
# Skill Title

## Overview
One or two sentences on what this skill does and why it matters.

## When to Use
- Triggering conditions (symptoms, task types)
- When NOT to use (exclusions)

## [Core Phases / The Workflow]
The main workflow broken into numbered phases.
Include code examples where they help.
Use ASCII flowcharts where decision points exist.

## [Specific Techniques / Patterns]
Detailed guidance for specific scenarios.
Code examples, anti-pattern tables, decision frameworks.

## Common Rationalizations
| Rationalization | Reality |
|---|---|
| Excuse agents use to skip steps | Why the excuse is wrong |

## Red Flags
- Behavioral patterns indicating the skill is being violated

## Verification
After completing the skill's process, confirm:
- [ ] Checklist of exit criteria
- [ ] Evidence requirements
```

## Section Purposes

### Overview
The "elevator pitch" for the skill. Answers: What does this skill do, and why should an agent follow it?

### When to Use
Helps agents and humans decide if this skill applies. Include both positive triggers ("Use when X") and negative exclusions ("Not for Y").

### Core Phases
The heart of the skill. Step-by-step workflow the agent follows. Must be specific and actionable, not vague advice.

Good: "Tab through every interactive element and confirm focus is always visible"
Bad: "Make sure the keyboard navigation works"

### Common Rationalizations
The most distinctive feature of well-crafted skills. These are excuses agents use to skip important steps, paired with rebuttals. They prevent the agent from rationalizing its way out of following the process.

### Red Flags
Observable signs that the skill is being violated. Useful during self-monitoring and review.

### Verification
The exit criteria. A checklist the agent uses to confirm the skill's process is complete. Every checkbox should be verifiable with evidence, not assumption.

## Writing Principles

1. **Process over knowledge.** Skills are workflows, not reference docs. Steps, not facts.
2. **Specific over general.** Concrete actions beat abstract advice.
3. **Evidence over assumption.** Every verification checkbox requires proof.
4. **Anti-rationalization.** Every step agents would skip needs a counter-argument in the rationalizations table.
5. **Progressive disclosure.** `SKILL.md` is the entry point. `examples.md` loads only when needed.
6. **Token-conscious.** Every section must justify its inclusion. If removing it would not change agent behavior, remove it.

## Naming Conventions

- Skill directories: `lowercase-hyphen-separated`
- Skill files: `SKILL.md` (always uppercase)
- Supporting files: `lowercase-hyphen-separated.md`

## Cross-Skill References

Reference other skills by name:

```markdown
For performance guidance, see the `performance-optimization` skill.
For security review, see the `security-and-hardening` skill.
```

Do not duplicate content between skills — reference instead.
