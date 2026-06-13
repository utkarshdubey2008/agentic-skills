# Using Agentic Skills with Cursor

## Setup

### Option 1: Rules Directory (Recommended)

Cursor supports a `.cursor/rules/` directory for project-specific rules:

```bash
mkdir -p your-project/.cursor/rules

cp /path/to/agentic-skills/skills/frontend-excellence/SKILL.md \
   your-project/.cursor/rules/frontend-excellence.md
```

Rules in this directory are automatically loaded into Cursor's context.

### Option 2: .cursorrules File

Create a `.cursorrules` file in your project root:

```bash
cat /path/to/agentic-skills/skills/frontend-excellence/SKILL.md > your-project/.cursorrules
```

## Usage Tips

1. **Don't load all skills at once** — Cursor has context limits. Load 1–2 skills relevant to the current task.
2. **Reference skills explicitly** — Tell Cursor "Follow the frontend-excellence rules for this component" to ensure it reads the loaded rules.
3. **Load examples on demand** — Paste `examples.md` content into the chat when you need calibration on what quality looks like.
