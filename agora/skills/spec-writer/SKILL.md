# Spec Writer

Draft a structured specification document from a feature description, design decision, or requirement.

## Usage
```
/spec-writer [type] [topic]
```

**type** (optional): the kind of spec to write — `prd`, `technical`, `adr`, `design`, `api`, `asset`. Defaults to the most appropriate type based on context.

**topic** (optional): what the spec is for. If omitted, asks the user.

## Spec Types

### PRD (Product Requirements Document)
- Problem statement and user need
- Goals and non-goals
- User stories / jobs to be done
- Success metrics
- Open questions

### Technical Spec
- Context and motivation
- Proposed solution and alternatives considered
- Data model / API changes
- Implementation plan
- Risks and mitigations
- Open questions

### ADR (Architectural Decision Record)
- Status (proposed / accepted / deprecated)
- Context: what situation prompted this decision
- Decision: what was decided
- Consequences: what this enables and what it forecloses

### Design Spec
- User flow and interaction model
- Component breakdown
- States and edge cases
- Accessibility considerations
- Open design questions

### Asset Spec (for pixel art, game assets, etc.)
- Asset type and purpose
- Resolution and grid size
- Color palette constraints
- Animation frames and timing (if applicable)
- Export format and naming convention

## Steps

1. **Clarify scope** — Ask any questions needed before drafting. Better to ask one good question than write the wrong spec.
2. **Draft the spec** — Use the appropriate template above. Fill in what's known, mark unknowns as `TBD` with context on what needs to be resolved.
3. **Review with user** — Present the draft and ask for gaps or corrections before finalizing.
4. **Save if requested** — Write to a `docs/` or `specs/` directory in the current project, or wherever the user directs.
