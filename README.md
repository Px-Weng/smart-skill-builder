# Smart Skill Builder (v4.1)

**Meta-skill that automates the creation of WorkBuddy skills** through a rigorous 5-phase quality-controlled pipeline.

## What It Does

Given a task description, this skill:
1. **Phase 1** — Analyzes intent: extracts domain, core task, trigger scenarios
2. **Phase 2** — Researches existing solutions across 4 sources (SkillHub, Vercel CLI, ClawHub, local marketplace)
3. **Phase 3** — Consults AI-simulated domain experts + optional external experts
4. **Phase 4** — Generates a complete SKILL.md with anti-cheat quality controls
5. **Phase 5** — Packages into `.zip`, installs, and tests

## v4.1 Changes

- **Built-in capability classification**: Split into `hard` (deterministic system tools) and `soft` (AI-native capabilities) for accurate gap analysis
- **4-source parallel search**: SkillHub API + Vercel Skills CLI + ClawHub + local marketplace, with merge & deduplication
- Fixed fallback branch dead code issue

## Installation

```bash
# From GitHub
git clone https://github.com/Px-Weng/smart-skill-builder.git
cp smart-skill-builder/SKILL.md ~/.workbuddy/skills/smart-skill-builder/

# Or import the zip directly
workbuddy skill import smart-skill-builder-v4.1.zip
```

## Trigger Phrases

- "create a skill for X"
- "build a skill that does Y"
- "I want a skill to..."
- 创建一个 skill / 帮我做一个 skill / 写一个技能

## Version History

| Version | Date | Key Changes |
|---------|------|-------------|
| v4.1 | 2026-06-22 | hard/soft classification, 4-source search |
| v4.0 | 2026-04-26 | Composite coverage check, Gap Fill mechanism |
| v3.5 | 2026-04-26 | Composite check, orchestrator support |
| v3.4 | 2026-04-26 | Dual expert review (GPT-4o + GLM), 17 fixes |
| v3.0 | 2026-04-25 | HARD STOP, GATE system, Anti-Cheat rules |

## License

MIT
