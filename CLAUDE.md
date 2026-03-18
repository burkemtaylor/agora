# Agora

A personal agent repository containing persona-based agents and shared skills for both professional and personal use.

## Structure

- `agents/` — Persona definitions. Each agent has a system prompt, a role, and references the skills it uses.
- `skills/` — SKILL.md slash commands. Invokable standalone or referenced by agents.
- `memory/` — Persistent learnings written by skills like `retrospective` and `memory-update`.

## Conventions

### Agents
- Agent files live at `agents/{name}.md`
- Each agent defines: role, core responsibilities, tone/style, and which skills it uses
- Agents are loaded by pasting their contents into a Claude Code session's context or referencing them in a project's CLAUDE.md

### Skills
- Each skill lives in its own directory: `skills/{skill-name}/SKILL.md`
- Skills are invoked as slash commands in Claude Code: `/skill-name`
- Skills should be self-contained — all instructions needed to execute are in the SKILL.md
- Skills that write learnings should always target the project's `memory/` directory

### Memory
- Memory files follow the format established in `.claude/projects/*/memory/`
- The `retrospective` skill is the primary way learnings get written to memory
- Memory should capture *why* decisions were made, not just what was done
