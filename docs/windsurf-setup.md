# Using Agentic Skills with Windsurf

## Setup

### Project Rules

Windsurf uses `.windsurfrules` for project-specific agent instructions:

```bash
cat /path/to/agentic-skills/skills/frontend-excellence/SKILL.md > your-project/.windsurfrules
```

### Global Rules

For skills you want across all projects:

1. Open Windsurf → Settings → AI → Global Rules
2. Paste the content of `skills/frontend-excellence/SKILL.md`

## Usage Tips

1. **Be selective** — Windsurf's context is limited. Choose skills that address your current work.
2. **Reference in conversation** — Paste additional skill content into the chat when working on specific tasks.
3. **Use examples as calibration** — Paste `skills/frontend-excellence/examples.md` into the chat and ask Windsurf to verify each pattern is being applied.
