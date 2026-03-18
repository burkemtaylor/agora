# Proposal: Forum Skill

**Status:** Implemented
**Date:** 2026-03-18
**Author:** Architect agent

---

## Problem

When facing a non-trivial decision -- a system design choice, a product direction, a technical tradeoff -- you want more than one perspective. Today, you can manually invoke individual agora agents (`agora:architect`, `agora:engineer`, etc.) and ask each one the same question, then synthesize their answers yourself. This is tedious, produces fragmented output, and the agents never actually respond to each other's arguments.

The forum skill automates this: you pose a question, a panel of agents deliberates, and you get a single synthesized document that captures the full landscape of perspectives, tensions, and a recommended path forward.

## User Experience

### Invocation

```
/agora:forum "Should we migrate from REST to GraphQL for our public API?"
```

Or with explicit panel selection:

```
/agora:forum --panel architect,engineer,product-manager "Should we use a monorepo or polyrepo?"
```

Or with a scope modifier:

```
/agora:forum --depth quick "Which test runner should we use?"
```

### What Happens

1. You run `/agora:forum` with a prompt.
2. The skill selects a panel of agents appropriate to the question (or uses ones you specified).
3. Each agent produces an independent position statement -- their analysis of the prompt from their persona's perspective.
4. A synthesis round identifies points of agreement, points of tension, and unresolved questions.
5. You receive a single structured document: the Forum Report.

### Output: The Forum Report

```markdown
# Forum Report: Should we migrate from REST to GraphQL?

## Panel
- Architect, Engineer, Product Manager

## Positions

### Architect
[2-4 paragraphs: their analysis, concerns, and recommendation]

### Engineer
[2-4 paragraphs: their analysis, concerns, and recommendation]

### Product Manager
[2-4 paragraphs: their analysis, concerns, and recommendation]

## Consensus
- [Points all panelists agree on]

## Tensions
- [Points where panelists disagree, with each side's reasoning]

## Unresolved Questions
- [Questions the forum surfaced but cannot answer without more information]

## Recommendation
[A synthesized recommendation that accounts for the tensions above.
 Not a lowest-common-denominator compromise -- a reasoned position
 that acknowledges tradeoffs.]
```

## Architecture

### Execution Model: Sequential, Not Parallel

The forum runs agents sequentially, not in parallel. This is a deliberate choice.

**Why not parallel?** Claude Code's Agent tool spawns subagents. Running them in parallel would mean each agent produces a position in isolation -- they never see each other's arguments. You get five monologues, not a deliberation. Parallel execution is faster but produces worse output.

**Why sequential?** Each agent after the first sees the positions already stated. This means the engineer can respond to the architect's concerns. The product manager can weigh in on the tension between engineering simplicity and architectural flexibility. The conversation compounds.

**Concrete execution flow:**

```
1. Forum skill receives prompt + panel selection
2. Agent 1 (e.g., Architect) is spawned via Agent tool
   - Input: the original prompt
   - Output: Position statement
3. Agent 2 (e.g., Engineer) is spawned via Agent tool
   - Input: the original prompt + Agent 1's position
   - Output: Position statement (may respond to Agent 1)
4. Agent 3 (e.g., Product Manager) is spawned via Agent tool
   - Input: the original prompt + all prior positions
   - Output: Position statement (may respond to prior positions)
5. Synthesis round (run by the forum skill itself, not a subagent)
   - Input: all position statements
   - Output: Consensus, Tensions, Unresolved Questions, Recommendation
```

The synthesis round is NOT delegated to another agent. The forum skill itself performs the synthesis, because no single persona should own the conclusion. The synthesis must be neutral -- it surfaces tensions rather than resolving them in favor of one perspective.

### Panel Selection

**Default behavior:** The skill infers which agents are relevant based on the prompt. A question about API design gets Architect + Engineer. A question about whether to build a feature gets Product Manager + Engineer + Designer. The selection heuristic is keyword- and intent-based, embedded in the SKILL.md instructions.

**Default panel mapping:**

| Prompt Signal | Panel |
|---|---|
| Architecture, system design, data model, infrastructure | architect, engineer |
| Feature decision, prioritization, user need | product-manager, designer, engineer |
| Code approach, implementation strategy, tooling | engineer, architect |
| UI/UX, user flow, accessibility | designer, product-manager |
| Broad/ambiguous question | architect, engineer, product-manager |

**Override:** The `--panel` flag lets you specify exactly which agents to include. This takes precedence over auto-selection.

**Panel size:** Minimum 2, maximum 4. Fewer than 2 is not a forum. More than 4 produces diminishing returns and excessive output length. The pixel-artist agent is excluded from auto-selection (it is a specialist) but can be explicitly included via `--panel`.

### Depth Control

The `--depth` flag controls how much each agent writes:

| Depth | Position Length | Use Case |
|---|---|---|
| `quick` | 1-2 paragraphs per agent | Gut-check, sanity check |
| `standard` (default) | 2-4 paragraphs per agent | Most decisions |
| `deep` | 4-8 paragraphs per agent | High-stakes architectural or product decisions |

### Integration with Existing Conventions

**Agents:** The forum spawns existing agora agents unmodified. It does not require changes to any agent definition. Each agent receives the prompt (and prior positions) as input and produces a text response -- exactly what they already do.

**Skills:** The forum is a new skill at `skills/forum/SKILL.md`. It does not depend on other skills, though the output of a forum could naturally feed into `/spec-writer` (to formalize the recommendation) or `/retrospective` (to capture the decision).

**Memory:** The forum skill does NOT automatically write to memory. The output is presented to the user, who can then decide to save it, act on it, or discard it. If you want to persist the decision, run `/retrospective` or `/memory-update` after.

**Plugin structure:** The skill follows the existing `skills/{name}/SKILL.md` convention with frontmatter for plugin discovery. No new infrastructure required.

## SKILL.md Structure (Conceptual)

The SKILL.md would contain:

1. **Frontmatter** -- name, description, argument-hint (matching existing convention)
2. **Usage section** -- invocation syntax, flags
3. **Panel selection logic** -- the keyword/intent mapping table above, expressed as instructions
4. **Execution steps** -- the sequential agent invocation protocol
5. **Synthesis instructions** -- how to identify consensus, tensions, and produce the recommendation
6. **Output template** -- the Forum Report format

The key design insight: the SKILL.md is itself the "orchestrator." It does not need a separate orchestrator agent. The skill instructions tell Claude Code how to invoke agents in sequence, accumulate their outputs, and synthesize the result. This is the same pattern as any other skill -- just with Agent tool calls as part of its execution.

## Design Decisions

### Why not a dedicated "moderator" agent?

A moderator agent would add indirection without adding value. The forum skill itself contains the synthesis instructions. A moderator agent would just be "an agent whose prompt is the synthesis instructions" -- that is the same thing with an extra layer of abstraction. Keep it simple.

### Why not a rebuttal round?

An earlier design considered having agents respond to each other in a second round (Agent 1 rebuts Agent 2's position, etc.). This was rejected because:

- It doubles the number of agent invocations (cost and latency).
- Sequential execution already lets later agents respond to earlier ones.
- The interesting output is the tension map, not agents arguing with each other.

If a future version wants deeper deliberation, a `--rounds 2` flag could add a rebuttal pass. But start without it.

### Why sequential order matters

The first agent to speak sets the frame. Later agents respond to that frame. This means agent ordering influences the output. The skill should order agents from broadest perspective to narrowest:

1. Product Manager (if present) -- frames the "why"
2. Architect (if present) -- frames the structural approach
3. Designer (if present) -- frames the user experience
4. Engineer (if present) -- frames the implementation reality

This ordering means the engineer always gets the last word before synthesis, which is appropriate: they are the one who has to build it, so their feasibility assessment should be informed by everything else.

### What about conflicting recommendations?

The synthesis does NOT force consensus. If the architect says "use microservices" and the engineer says "use a monolith," the Forum Report should state both positions clearly, explain the tradeoff, and make a recommendation that acknowledges the tension. The recommendation section should be willing to pick a side, but must explain what is being traded away.

### Output destination

The Forum Report is printed to stdout (the conversation). It is not automatically written to a file. The user can ask to save it, or pipe it into `/spec-writer` to formalize the decision. This matches the pattern of other skills -- they produce output in the conversation first, save on request.

## Edge Cases

**Single-agent panel:** If `--panel` specifies only one agent, the skill should warn that a forum requires at least two perspectives and suggest running the agent directly instead.

**Question too vague:** If the prompt is too vague for panel auto-selection (e.g., "what should we do?"), the skill should ask for clarification before proceeding.

**Agent unavailable:** If a specified agent does not exist in `agora/agents/`, the skill should fail with a clear error listing available agents.

**Very long positions:** The depth flag bounds this, but the synthesis instructions should also note that positions should be substantive, not exhaustive. The forum is for decision-making, not documentation.

**Non-technical questions:** The forum works for any question where multiple perspectives add value. "Should we hire a contractor or build in-house?" is a valid forum question even though it is not purely technical. The panel selection heuristic should handle this gracefully by defaulting to the broadest panel.

## Cost and Latency

Each agent invocation is a full Agent tool call. For a 3-agent standard-depth forum:

- 3 agent calls (sequential)
- 1 synthesis pass
- Estimated wall time: 60-120 seconds depending on model and prompt complexity

This is acceptable for a deliberation tool. You use the forum when the question is worth spending 2 minutes on. For quick decisions, use `--depth quick` or just ask a single agent directly.

## Implementation Path

1. Create `skills/forum/SKILL.md` with the full skill definition
2. Update `README.md` to add the forum skill to the skills table
3. Test with 3-4 representative prompts across different panel compositions
4. No changes needed to existing agents or skills

The entire implementation is a single SKILL.md file. No code, no infrastructure, no agent modifications. That is the advantage of agora's architecture -- the skill is just instructions, and Claude Code's Agent tool is the execution engine.
