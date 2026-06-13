# Using Agentic Skills with OpenCode

## Overview

OpenCode supports custom slash commands but does not have a native plugin system with automatic skill routing. Instead, this repo achieves skill enforcement through:

- A strong system prompt (`AGENTS.md` at the repo root)
- The built-in `skill` tool
- Consistent skill discovery from the `/skills` directory

This creates an **agent-driven workflow** where skills are selected and executed automatically based on intent.

## Installation

```bash
git clone https://github.com/utkarshdubey2008/agentic-skills.git
```

Open the project in OpenCode. Ensure `AGENTS.md` and the `skills/` directory are present in your workspace. No additional installation is required.

## How It Works

### Automatic Skill Invocation

The agent evaluates every request and maps it to the appropriate skill:

- "build a component" / "fix the UI" / "review this interface" → `frontend-excellence`

The user does not need to explicitly request a skill.

### Lifecycle Mapping

The `AGENTS.md` file teaches OpenCode to map intent to skills automatically. The agent follows the skill's phases strictly and does not skip verification.

## Usage Examples

**UI work:**
```
"Build a task list component with loading and empty states"
```
Agent behavior: invokes `frontend-excellence`, works through all 8 phases, runs visual QA checklist before finishing.

**Review:**
```
"Review this component for quality"
```
Agent behavior: invokes `frontend-excellence`, applies the visual QA checklist, reports findings by phase.

## Limitations

- No native slash commands (handled via intent mapping in `AGENTS.md` instead)
- Skill invocation depends on model compliance with `AGENTS.md`
