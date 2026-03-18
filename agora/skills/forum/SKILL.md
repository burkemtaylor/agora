---
name: forum
description: "Convene a panel of agents to deliberate on a question and produce a synthesized recommendation"
argument-hint: "[--panel agent1,agent2,...] [--depth quick|standard|deep] <prompt>"
---

# Forum

Convene a panel of agora agents to deliberate on a question from multiple perspectives. Each agent produces a position statement, and the forum synthesizes their views into a structured report with consensus, tensions, and a recommendation.

## Usage
```
/forum [options] <prompt>
```

**prompt** (required): The question or decision to deliberate on.

**--panel** (optional): Comma-separated list of agents to include. Overrides auto-selection. Available agents: `architect`, `engineer`, `designer`, `product-manager`, `pixel-artist`.

**--depth** (optional): Controls position length. One of `quick`, `standard` (default), `deep`.

## Steps

### 1. Parse Input

Extract the prompt, panel, and depth from the user's input.

- If no prompt is provided, ask the user: "What question should the forum deliberate on?"
- If `--panel` is specified, validate that each agent exists. Available agents: `architect`, `engineer`, `designer`, `product-manager`, `pixel-artist`. If an agent is not recognized, fail with: "Unknown agent: [name]. Available agents: architect, engineer, designer, product-manager, pixel-artist."
- If `--panel` specifies only one agent, respond: "A forum requires at least two perspectives. To get a single agent's take, invoke it directly (e.g., `agora:architect`)."
- If `--depth` is not specified, default to `standard`.

### 2. Select Panel

If `--panel` was specified, use those agents in the order listed below (not the order specified by the user).

If no `--panel` was specified, infer the panel from the prompt using these heuristics:

| Prompt signals | Panel |
|---|---|
| Architecture, system design, data model, infrastructure, scalability, services | architect, engineer |
| Feature decision, prioritization, user need, product direction, roadmap | product-manager, designer, engineer |
| Code approach, implementation strategy, tooling, library choice, testing | engineer, architect |
| UI/UX, user flow, accessibility, interaction design, visual design | designer, product-manager |
| Broad, ambiguous, or multi-dimensional question | product-manager, architect, engineer |

If the prompt does not clearly match any category, use the broad/ambiguous default: product-manager, architect, engineer.

### 3. Order the Panel

Regardless of how agents were selected, order them in this sequence (skip agents not in the panel):

1. **Product Manager** — frames the "why" and user need
2. **Architect** — frames the structural approach
3. **Designer** — frames the user experience
4. **Engineer** — frames implementation reality (always speaks last before synthesis)
5. **Pixel Artist** — specialist input (only when explicitly included)

This ordering ensures each subsequent agent can respond to the framing established by earlier agents, and the engineer — who has to build it — gets the final word before synthesis.

### 4. Determine Position Length

| Depth | Instructions to each agent |
|---|---|
| `quick` | "Keep your position to 1-2 concise paragraphs. Focus on your strongest argument and biggest concern." |
| `standard` | "Write 2-4 paragraphs. Cover your analysis, key concerns, and your recommendation." |
| `deep` | "Write 4-8 paragraphs. Provide thorough analysis including tradeoffs, risks, alternatives considered, and a detailed recommendation." |

### 5. Invoke Agents Sequentially

For each agent in the ordered panel, spawn it using the Agent tool with `subagent_type` set to `agora:{agent-name}`.

**First agent prompt:**
```
You are participating in a forum deliberation. The question being discussed is:

"{prompt}"

You are the first to speak. Provide your position on this question from your perspective.
{depth instruction}

Do not produce a full report or synthesis — just your position statement. Be direct and opinionated. State your recommendation clearly.
```

**Subsequent agent prompts:**
```
You are participating in a forum deliberation. The question being discussed is:

"{prompt}"

The following positions have already been stated:

{all prior position statements, each labeled with the agent name}

Provide your position on this question from your perspective. You may agree with, challenge, or build on the positions above. Do not simply summarize what others said — add your own analysis.
{depth instruction}

Do not produce a full report or synthesis — just your position statement. Be direct and opinionated. State your recommendation clearly.
```

Collect each agent's response as their position statement.

### 6. Synthesize

After all agents have spoken, YOU (the forum skill, not a subagent) synthesize the positions. This synthesis must be neutral — it surfaces the landscape of views rather than favoring one persona.

Analyze the positions to identify:

- **Consensus**: Points where all panelists agree, whether explicitly or implicitly.
- **Tensions**: Points where panelists disagree. State each side's reasoning fairly. Do not resolve tensions by splitting the difference — state the tradeoff clearly.
- **Unresolved Questions**: Questions that the forum surfaced but cannot answer without more information from the user or further investigation.
- **Recommendation**: A synthesized recommendation that accounts for the tensions above. This should be a reasoned position that picks a direction, not a lowest-common-denominator compromise. State clearly what is being traded away.

### 7. Produce the Forum Report

Output the report in this format:

```markdown
# Forum Report: {prompt}

## Panel
{comma-separated list of agents who participated}

## Positions

### {Agent 1 Name}
{their position statement}

### {Agent 2 Name}
{their position statement}

[...for each agent]

## Consensus
- {point of agreement}
- {point of agreement}

## Tensions
- **{tension topic}**: {Agent A} argues {X} because {reason}. {Agent B} argues {Y} because {reason}.

## Unresolved Questions
- {question that needs more information}

## Recommendation
{synthesized recommendation — picks a direction, acknowledges tradeoffs}
```

### 8. Present and Offer Next Steps

After presenting the Forum Report, offer:

- "Run `/spec-writer` to formalize this recommendation into a spec"
- "Run `/retrospective` to capture this decision in memory"
- "Save this report to a file"

Do NOT automatically write to memory or save to a file. Let the user decide.
