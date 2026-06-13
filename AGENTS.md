# AGENTS.md

This file provides guidance to AI coding agents (Claude Code, Cursor, Copilot, OpenCode, Gemini CLI, etc.) when working with code in this repository.

## Repository Overview

A collection of production-grade agent skills for AI coding agents. Skills are structured workflows with steps, verification gates, and anti-rationalization tables that help agents build higher-quality software.

## OpenCode Integration

OpenCode uses an **agent-driven skill execution model** powered by the `skill` tool and the `/skills` directory in this repo.

### Core Rules

- If a task matches a skill, you MUST invoke it
- Skills are located at `skills/<skill-name>/SKILL.md`
- Never implement directly if a skill applies
- Always follow the skill instructions exactly — do not partially apply them

### Intent → Skill Mapping

The agent should automatically map user intent to skills:

- UI work / frontend / component / interface → `frontend-excellence`

### Execution Model

For every request:

1. Determine if any skill applies (even 1% chance)
2. Load the appropriate `SKILL.md`
3. Follow the skill workflow strictly
4. Only proceed to implementation after required steps are complete

### Anti-Rationalization

The following thoughts are incorrect and must be ignored:

- "This is too small for a skill"
- "I can just quickly implement this"
- "I'll gather context first, then decide"

Correct behavior: always check for and use skills first.

## Orchestration: Skills and Personas

Skills (`skills/<name>/SKILL.md`) are workflows with steps and exit criteria. They define *how* to do the work. They are mandatory when an intent matches.

## Creating a New Skill

### Directory Structure

```
skills/
  {skill-name}/
    SKILL.md              # Required: skill definition
    examples.md           # Optional: reference examples loaded on demand
```

### SKILL.md Format

```markdown
---
name: {skill-name}
description: {One sentence describing what the skill does. Use when [trigger conditions].}
---

# {Skill Title}

## Overview
## When to Use
## [Core Process / Phases]
## Common Rationalizations
## Red Flags
## Verification
```

### Best Practices for Context Efficiency

- Keep `SKILL.md` focused — put long examples in `examples.md`
- Write specific descriptions so the agent knows exactly when to activate the skill
- Use progressive disclosure — supporting files load only when needed
