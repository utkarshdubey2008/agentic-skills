# Getting Started with Agentic Skills

Agentic Skills works with any AI coding agent that accepts Markdown instructions. This guide covers the universal approach. For tool-specific setup, see the dedicated guides.

## How Skills Work

Each skill is a Markdown file (`SKILL.md`) that describes a specific engineering workflow. When loaded into an agent's context, the agent follows the workflow — including verification steps, anti-patterns to avoid, and exit criteria.

**Skills are not reference docs.** They are step-by-step processes the agent follows.

## Quick Start (Any Agent)

### 1. Clone the repository

```bash
git clone https://github.com/utkarshdubey2008/agentic-skills.git
```

### 2. Choose a skill

Browse the `skills/` directory. Each subdirectory contains a `SKILL.md` with:
- **When to use** — triggers that indicate this skill applies
- **Process** — step-by-step workflow with phases
- **Verification** — how to confirm the work is done correctly
- **Common rationalizations** — excuses the agent might use to skip steps
- **Red flags** — signs the skill is being violated

### 3. Load the skill into your agent

Copy the relevant `SKILL.md` content into your agent's system prompt, rules file, or conversation:

**System prompt:** Paste the skill content at the start of the session.

**Rules file:** Add skill content to your project's rules file (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, etc.).

**Conversation:** Reference the skill when giving instructions: "Follow the frontend-excellence skill for this component."

### 4. Follow the phases

Each skill is broken into numbered phases. Work through them in order. Do not skip verification.

## Skill Anatomy

Every skill follows the same structure:

```
YAML frontmatter (name, description)
├── Overview         — What this skill does and why it matters
├── When to Use      — Triggers and exclusions
├── Core Phases      — Step-by-step workflow with code examples
├── Rationalizations — Excuses and rebuttals
├── Red Flags        — Signs the skill is being violated
└── Verification     — Exit criteria checklist
```

See [skill-anatomy.md](skill-anatomy.md) for the full specification.

## Tips

1. **Load skills selectively** — more context is not always better; load what is relevant to the current task
2. **Do not skip verification** — the checklist at the end of each skill is the whole point
3. **Use examples.md for calibration** — when unsure what quality looks like, read the supporting examples file
