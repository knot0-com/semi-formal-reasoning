# Semi-Formal Code Reasoning

**Structured code verification, fault localization, and semantic analysis — without running tests.**

Based on the [Agentic Code Reasoning](https://arxiv.org/abs/2603.01896) paper (Ugare & Chandra, Meta, 2026) which showed 10-12pp accuracy gains over unstructured chain-of-thought reasoning.

## What it does

Forces your coding agent to reason about code using semi-formal certificates instead of free-form chain-of-thought. The agent must:

- State explicit premises with file:line citations
- Trace actual execution paths (not assume from function names)
- Check for shadowed names, imports, and indirect calls
- Derive formal conclusions from evidence

Three task types:

| Task | Trigger | Paper accuracy |
|-|-|-|
| Patch verification | "verify my changes", "is this diff correct" | 88-93% |
| Fault localization | "find the bug", "why does this test fail" | 72% Top-5 |
| Code QA | "how does this work", "explain this behavior" | 87% |

## Install

### Claude Code
```bash
git clone https://github.com/knot0-com/semi-formal-reasoning.git ~/.claude/skills/semi-formal-reasoning
```

### OpenAI Codex
```bash
git clone https://github.com/knot0-com/semi-formal-reasoning.git ~/.codex/skills/semi-formal-reasoning
```

### Gemini CLI
```bash
git clone https://github.com/knot0-com/semi-formal-reasoning.git ~/.gemini/skills/semi-formal-reasoning
```

## Usage

Once installed, trigger with:
```
> "verify my changes before I commit"
> "find the bug causing this test failure"
> "how does this function actually work?"
```

Or invoke directly: `/semi-formal-reasoning`

## What's Included

```
semi-formal-reasoning/
├── SKILL.md              # Core workflow and method
├── references/
│   └── templates.md      # Full semi-formal templates for all 3 task types
├── marketplace.json      # For SkillsMP auto-discovery
├── LICENSE               # MIT
└── README.md             # This file
```

## The Key Insight

From the paper's Django-13670 example: two patches for 2-digit year formatting looked equivalent. Standard reasoning said yes. Semi-formal analysis discovered that `format` was shadowed by a module-level function — Patch 1 would raise AttributeError. The template forced the agent to trace the actual call instead of assuming Python's builtin.

## Paper

[Agentic Code Reasoning](https://arxiv.org/abs/2603.01896) — Shubham Ugare, Satish Chandra (Meta, 2026)

## License

MIT
