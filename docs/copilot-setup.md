# Using Agentic Skills with GitHub Copilot

## Setup

### Copilot Instructions

Add skill content to `.github/copilot-instructions.md` in your project:

```bash
mkdir -p your-project/.github
cat /path/to/agentic-skills/skills/frontend-excellence/SKILL.md \
    >> your-project/.github/copilot-instructions.md
```

### Skills Directory

Copilot supports a `.github/skills/` directory:

```bash
mkdir -p your-project/.github/skills/frontend-excellence
cp /path/to/agentic-skills/skills/frontend-excellence/SKILL.md \
   your-project/.github/skills/frontend-excellence/SKILL.md
```

## Recommended Configuration

### .github/copilot-instructions.md

```markdown
# UI Quality Standards

## Before starting any UI task
Follow the frontend-excellence skill workflow:
1. Identify all states (loading, empty, error, populated)
2. Plan visual hierarchy before writing markup
3. Use design system tokens — no invented spacing values
4. Ensure all interactive elements have visible focus states
5. Test keyboard navigation before calling the task complete

## The bar
"Would a user who cares about quality notice something is wrong here?"
```

## Usage Tips

1. **Keep instructions concise** — Copilot instructions work best when focused. The full skill is in the `.github/skills/` directory; use `copilot-instructions.md` for key rules only.
2. **Reference in chat** — When working on UI, paste the relevant skill phase into Copilot Chat for context.
