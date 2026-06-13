# Agentic Skills

**Production-grade agent skills for AI coding agents.**

Skills encode the workflows, quality gates, and best practices that senior engineers use when building software — packaged so AI agents follow them consistently across every phase of development.

```
  DEFINE          PLAN           BUILD          VERIFY         REVIEW          SHIP
 ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐
 │ Idea │ ───▶ │ Spec │ ───▶ │ Code │ ───▶ │ Test │ ───▶ │  QA  │ ───▶ │  Go  │
 │Refine│      │  PRD │      │ Impl │      │Debug │      │ Gate │      │ Live │
 └──────┘      └──────┘      └──────┘      └──────┘      └──────┘      └──────┘
```

---

## Skills

| Skill | What It Does | Use When |
|---|---|---|
| [frontend-excellence](skills/frontend-excellence/SKILL.md) | Builds, reviews, and improves UIs to production quality. Covers visual hierarchy, all component states, interaction design, accessibility, responsive layout, and visual QA. | Building or reviewing any user-facing interface |

---

## Quick Start

<details>
<summary><b>Claude Code (recommended)</b></summary>

**Clone and use locally:**

```bash
git clone https://github.com/utkarshdubey2008/agentic-skills.git
claude --plugin-dir /path/to/agentic-skills
```

Or reference in your `CLAUDE.md`:

```markdown
Read and apply skills/frontend-excellence/SKILL.md when building or reviewing UI.
```

</details>

<details>
<summary><b>Cursor</b></summary>

Copy any `SKILL.md` into `.cursor/rules/` in your project:

```bash
mkdir -p your-project/.cursor/rules
cp skills/frontend-excellence/SKILL.md your-project/.cursor/rules/frontend-excellence.md
```

Cursor auto-discovers rules in that directory. See [docs/cursor-setup.md](docs/cursor-setup.md).

</details>

<details>
<summary><b>Gemini CLI</b></summary>

Install from the repo:

```bash
gemini skills install https://github.com/utkarshdubey2008/agentic-skills.git --path skills
```

Or from a local clone:

```bash
git clone https://github.com/utkarshdubey2008/agentic-skills.git
gemini skills install ./agentic-skills/skills/
```

See [docs/gemini-cli-setup.md](docs/gemini-cli-setup.md).

</details>

<details>
<summary><b>Windsurf</b></summary>

Add skill contents to `.windsurfrules` in your project root:

```bash
cat skills/frontend-excellence/SKILL.md >> your-project/.windsurfrules
```

See [docs/windsurf-setup.md](docs/windsurf-setup.md).

</details>

<details>
<summary><b>OpenCode</b></summary>

Uses agent-driven skill execution via `AGENTS.md` and the `skill` tool.

```bash
git clone https://github.com/utkarshdubey2008/agentic-skills.git
```

Open the project in OpenCode — the `AGENTS.md` at the repo root teaches the agent to discover and apply skills automatically. See [docs/opencode-setup.md](docs/opencode-setup.md).

</details>

<details>
<summary><b>GitHub Copilot</b></summary>

Add skill content to `.github/copilot-instructions.md`:

```bash
mkdir -p your-project/.github
cat skills/frontend-excellence/SKILL.md >> your-project/.github/copilot-instructions.md
```

See [docs/copilot-setup.md](docs/copilot-setup.md).

</details>

<details>
<summary><b>Kiro IDE & CLI</b></summary>

Skills for Kiro reside under `.kiro/skills/` at project or global level:

```bash
mkdir -p your-project/.kiro/skills
cp skills/frontend-excellence/SKILL.md your-project/.kiro/skills/
```

Kiro also supports `AGENTS.md`. See [Kiro docs](https://kiro.dev/docs/skills/).

</details>

<details>
<summary><b>Codex / Other Agents</b></summary>

Skills are plain Markdown — they work with any agent that accepts system prompts or instruction files. Paste the contents of any `SKILL.md` into your agent's context. See [docs/getting-started.md](docs/getting-started.md).

</details>

---

## How Skills Work

Every skill follows a consistent anatomy:

```
┌─────────────────────────────────────────────────┐
│  SKILL.md                                       │
│                                                 │
│  ┌─ Frontmatter ─────────────────────────────┐  │
│  │ name: lowercase-hyphen-name               │  │
│  │ description: Guides agents through [task].│  │
│  │              Use when…                    │  │
│  └───────────────────────────────────────────┘  │
│  Overview         → What this skill does        │
│  When to Use      → Triggering conditions       │
│  Process          → Step-by-step workflow       │
│  Rationalizations → Excuses + rebuttals         │
│  Red Flags        → Signs something is wrong    │
│  Verification     → Evidence requirements       │
└─────────────────────────────────────────────────┘
```

**Key design choices:**

- **Process, not prose.** Skills are workflows agents follow, not reference docs they read. Each has phases, checkpoints, and exit criteria.
- **Anti-rationalization.** Every skill includes a table of common excuses agents use to skip steps with documented counter-arguments.
- **Verification is non-negotiable.** Every skill ends with evidence requirements. "Seems right" is never sufficient.
- **Progressive disclosure.** The `SKILL.md` is the entry point. Supporting files like `examples.md` load only when needed, keeping token usage minimal.

---

## Project Structure

```
agentic-skills/
├── skills/
│   └── frontend-excellence/    # Build phase — UI quality
│       ├── SKILL.md            # Main skill definition
│       └── examples.md         # Annotated before/after examples
├── docs/                       # Setup guides per tool
│   ├── getting-started.md
│   ├── cursor-setup.md
│   ├── gemini-cli-setup.md
│   ├── windsurf-setup.md
│   ├── opencode-setup.md
│   └── copilot-setup.md
├── .claude/
│   └── commands/               # Slash commands for Claude Code
├── .gemini/
│   └── commands/               # Slash commands for Gemini CLI
├── AGENTS.md                   # OpenCode / agent orchestration rules
├── plugin.json                 # Plugin manifest
├── CONTRIBUTING.md
└── LICENSE
```

---

## Why Agentic Skills?

AI coding agents default to the shortest path — which often means skipping accessibility, states, visual QA, and the practices that make UI feel production-quality. Agentic Skills gives agents structured workflows that enforce the same discipline senior design engineers bring to production interfaces.

Each skill encodes hard-won judgment: *when* a loading state is required, *what* makes an empty state useful, *how* to audit keyboard navigation, and *when* a hover state is not optional. These are not generic tips — they are opinionated, process-driven workflows that separate production-quality UI from prototype-quality UI.

---

## Contributing

Skills should be **specific** (actionable steps, not vague advice), **verifiable** (clear exit criteria with evidence requirements), **battle-tested** (based on real workflows), and **minimal** (only what is needed to guide the agent).

See [docs/skill-anatomy.md](docs/skill-anatomy.md) for the format spec and [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## Credits

The skill structure, anatomy, and repository conventions in this project are heavily inspired by [Addy Osmani's agent-skills](https://github.com/addyosmani/agent-skills) — a production-grade collection of engineering skills for AI coding agents. The frontmatter format, section anatomy (Overview → When to Use → Process → Rationalizations → Red Flags → Verification), progressive disclosure pattern, and multi-tool setup approach all originate from that work.

The `frontend-excellence` skill builds on that foundation with original content focused on design fidelity, interaction quality, human-like copy, and visual QA.

---

## License

MIT — use these skills in your projects, teams, and tools. See [LICENSE](LICENSE).
