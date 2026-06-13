# Using Agentic Skills with Gemini CLI

## Setup

### Option 1: Install as Skills (Recommended)

Gemini CLI has a native skills system that auto-discovers `SKILL.md` files in `.gemini/skills/` or `.agents/skills/` directories.

**Install from the repo:**

```bash
gemini skills install https://github.com/utkarshdubey2008/agentic-skills.git --path skills
```

**Or from a local clone:**

```bash
git clone https://github.com/utkarshdubey2008/agentic-skills.git
gemini skills install ./agentic-skills/skills/
```

Verify with:

```
/skills list
```

### Option 2: GEMINI.md (Persistent Context)

For skills you want always loaded as persistent project context:

```bash
cat /path/to/agentic-skills/skills/frontend-excellence/SKILL.md > GEMINI.md
```

Or modularize with imports:

```markdown
# Project Instructions

@skills/frontend-excellence/SKILL.md
```

Use `/memory show` to verify loaded context and `/memory reload` to refresh after changes.

## Slash Commands

The repo ships a slash command under `.gemini/commands/` for Gemini CLI auto-discovery. Run it from the project root:

| Command | What it does |
|---|---|
| `/frontend` | Build or review UI using the frontend-excellence skill |

## Usage Tips

1. **Prefer skills over GEMINI.md** — Skills activate on demand and keep your context window focused.
2. **Use `/frontend` for UI work** — The slash command loads the full skill automatically.
3. 
