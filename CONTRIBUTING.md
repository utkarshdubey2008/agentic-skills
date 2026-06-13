# Contributing to Agentic Skills

Thanks for your interest in contributing. Skills are the core of this project — quality over quantity.

## Adding a New Skill

1. Create a directory under `skills/` with a kebab-case name matching the skill
2. Add a `SKILL.md` following the format in [docs/skill-anatomy.md](docs/skill-anatomy.md)
3. Include YAML frontmatter with `name` and `description` fields
4. Ensure the `description` starts with what the skill does (third person), then includes one or more `Use when` trigger conditions
5. Update the skills table in `README.md`

### Skill Quality Bar

Skills should be:

- **Specific** — Actionable steps, not vague advice
- **Verifiable** — Clear exit criteria with evidence requirements
- **Battle-tested** — Based on real engineering workflows, not theoretical ideals
- **Minimal** — Only the content needed to guide the agent correctly

### Structure

Every skill must have:

- `SKILL.md` in the skill directory
- Valid YAML frontmatter with `name` and `description`

Skills follow this section anatomy:

- **Overview** — What this skill does and why it matters
- **When to Use** — Triggering conditions and exclusions
- **Core Phases** — Step-by-step workflow with code examples
- **Common Rationalizations** — Excuses agents use to skip steps, with rebuttals
- **Red Flags** — Warning signs the skill is being violated
- **Verification** — Checklist of exit criteria requiring evidence

### What Not to Do

- Don't duplicate content between skills — reference other skills instead
- Don't add skills that are vague advice instead of actionable processes
- Don't create `examples.md` unless it would exceed ~80 lines inline
- Don't put reference material inside skill directories — use a top-level `references/` directory if one is needed

## Modifying Existing Skills

- Keep changes focused and minimal
- Preserve the existing structure and tone
- Do not reduce specificity — vague advice is worse than no advice
- Test that YAML frontmatter remains valid after edits

## Reporting Issues

Open an issue if you find:

- A skill that gives incorrect or outdated guidance
- Missing coverage for a common engineering workflow
- Inconsistencies between skills or documentation

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
