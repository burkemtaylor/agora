# Agora

A personal Claude Code plugin containing persona-based agents and shared skills for professional and personal use.

## Agents

### Professional
| Agent | Description |
|-------|-------------|
| `engineer` | Senior software engineer — clean, production-ready code |
| `architect` | Software architect — system design and technical strategy |
| `designer` | Product designer — UX, visual craft, and design systems |
| `product-manager` | Product manager — requirements, prioritization, and alignment |
| `pixel-artist` | Pixel artist — sprite art, tilesets, and asset pipelines |

### Personal
| Agent | Description |
|-------|-------------|
| `assistant` | Personal assistant — scheduling, planning, and organization |

## Skills

### Shared
| Skill | Command | Description |
|-------|---------|-------------|
| Retrospective | `/agora:retrospective` | Structured retrospective that writes learnings to memory |
| Memory Update | `/agora:memory-update` | Capture a specific learning or decision mid-conversation |

### Professional
| Skill | Command | Description |
|-------|---------|-------------|
| Code Review | `/agora:code-review` | Multi-dimension code review |
| Spec Writer | `/agora:spec-writer` | Draft PRDs, technical specs, ADRs, design specs, and asset specs |

### Personal
| Skill | Command | Description |
|-------|---------|-------------|
| Daily Plan | `/agora:daily-plan` | Generate a structured daily plan |

## Installation

Add to `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "agora": {
      "source": {
        "source": "github",
        "repo": "burkemtaylor/agora"
      }
    }
  },
  "enabledPlugins": {
    "agora@agora": true
  }
}
```
