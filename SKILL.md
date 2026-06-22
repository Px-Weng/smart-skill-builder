---
name: smart-skill-builder
description: >
  This skill should be used when the user wants to "create a skill",
  "make a skill", "build a skill", "new skill", "help me build",
  "design a skill", "plan a skill", "write a skill",
  "I want a skill that does X", "turn this workflow into a skill",
  "how do I make a skill for...", "create a new skill from scratch",
  "build me a custom skill", "create skill", "can you make a skill",
  "whip up a skill", "做一个 skill", "新 skill", "帮我制作",
  "搞个skill", "弄个skill", "整一个skill",
  "设计一个 skill", "规划 skill", "编写 skill",
  "我想做一个能 XXX 的 skill", "我要一个能 XXX 的功能",
  "把这个流程做成 skill", "帮我建个 skill",
  "创建一个新的 skill", "自定义 skill",
  "zhi zuo yi ge skill", "zuo yi ge skill",
  "bang wo zhi zuo", "chuang jian skill". Orchestrates:
  analyze intent → search existing solutions (HARD STOP on direct
  or composite matches) → expert consultation (AI-simulated by default) →
  generate via skill-creator → deliver with test prompts. Fills the gap between
  skill-creator (manual tool) and find-skills (search-only).
description_zh: >
  当用户想创建新 skill 但需求尚未完全明确时使用本技能。
  协调 5 阶段全自动流程：分析意图 → 搜索现有方案（发现成熟方案或已有
  skill 组合可覆盖时硬停止提醒用户先试用）→ 专家咨询优化提示词（AI 默认自模拟，可升级为外部专家）
  → 用 skill-creator 规范生成 → 打包前审查 → 交付测试。
  填补 skill-creator（手动工具）和 find-skills（仅搜索）之间的空白。
  触发词：创建 skill、做一个 skill、新 skill、帮我制作、搞个 skill、弄个 skill、
  整一个 skill、设计 skill、规划 skill、编写 skill、自定义 skill、
  build a skill、create a skill、can you make a skill、whip up a skill。
_localized_zh: "true"
---

# Smart Skill Builder — Meta-Skill for Skill Creation

**Version: v4.1**

A meta-skill that orchestrates a rigorous 5-phase end-to-end workflow for
creating high-quality, discoverable skills. This skill IS a meta-skill —
its purpose is creating other skills.

## Purpose

`skill-creator` provides a manual creation tool; `find-skills` provides a
search tool. Neither searches before building nor consults experts to refine
requirements. This skill combines both into an automated pipeline that
prevents wheel reinvention and produces skills with superior trigger
accuracy, deeper domain knowledge, and proper resource organization.

## When to Use

- User asks to create, build, make, design, plan, or write a new skill
- User describes a capability gap and wants a skill for it
- User wants to turn a workflow, expertise, or repeated task into an installable skill

**Out of scope:** Modifying an existing installed skill. For that, use
`skill-creator` directly — this workflow is designed for creating NEW skills
from scratch. Phase 2's search results can still inform what to change, but
Phases 3-5 should not be followed for modifications.

## Workflow

Execute the following phases **in strict order**. Do NOT skip phases unless
the user explicitly requests skipping (see Edge Case #7).

**Self-reference note:** When this skill is used to create ITSELF (or any
other meta-skill), the same 5-phase workflow applies — treat the creation
request as any other and execute each phase faithfully.

---

## Anti-Cheat Rules (Read Before Every Phase)

These rules override default AI behavior. They exist because AI agents have
a strong bias toward "helpful continuation" — doing more than asked, combining
steps, and skipping gates in the name of efficiency. This workflow's quality
depends on resisting that bias.

1. **NEVER load skill-creator or write SKILL.md before completing Phases 1-3.**
   Even if the user's request seems detailed enough to build immediately,
   the search-then-consult pattern exists to catch duplicates and improve
   trigger quality. There are NO exceptions to this rule.

2. **NEVER proceed past a GATE or HARD STOP without explicit user input.**
   If the user's last message was informational (not a decision/confirmation),
   re-state the pending question and wait. Do NOT guess what they want.
   _(Exception: see Rule #4 for the specific behavior at HARD STOP points —
   Rule #4 takes precedence over this rule in HARD STOP scenarios.)_

3. **NEVER combine phases.** Each phase must produce its designated output
   artifact (Intent Summary, Search Report, Skill Brief) before the next phase
   begins. Present each artifact visibly to the user.

4. **NEVER auto-answer for the user at HARD STOP points.** When a Direct Match
   is found, present options and WAIT. The AI must not assume or predict the
   user's choice. After presenting the match, STOP completely — do not begin
   any Phase 3 activities, do not prepare questions, do nothing but wait.
If the user sends an off-topic or non-decision message after the HARD STOP,
respond only with a brief reminder of the pending decision, using the same
option wording as the original HARD STOP message — nothing more.

5. **When in doubt, slower is better.** A skill taking 10 minutes done right beats
   one taking 2 minutes that nobody can trigger. Quality > speed, always.

---

## Phase 1: Analyze Intent

From the user's raw request, extract four fields:

| Field | Description |
|-------|-------------|
| `domain` | What field/topic? (e.g., finance, design, automation, writing) |
| `core_task` | What specific actions should the skill enable? |
| `trigger_scenarios` | In what situations would the user invoke this skill? |
| `user_confidence` | High (clear spec) / Medium (rough idea) / Low (vague) |

**Output artifact:** Present an **Intent Summary** adjusted to the `user_confidence`
level. The format and depth differ based on how clear the user's request is:

**GATE-1 Smart Tiers (based on `user_confidence`):**

- **High** (user gave a clear, specific request): One-sentence confirmation.
  Example: "You want a `[domain]` skill that `[core_task]`. Got it — proceeding?"
  The user already knows what they want; a table or paragraph is patronizing.

- **Medium** (user has a rough idea, some ambiguity): Full Intent Summary table
  (domain, core_task, trigger_scenarios) + 2-3 sentence natural-language
  summary. Present as a table and ask "Is this correct?"
  This is the default format — thorough enough to catch gaps without wasting
  the user's time.

- **Low** (request is vague or underspecified): **First ask 1-2 clarifying
  questions** to fill critical gaps, THEN present the Intent Summary.
  Why: the user came to the AI because they don't fully know what they need.
  Showing them a summary of their own vagueness doesn't help — they can't
  judge whether it's "correct." Instead, help them crystallize the need first.
  Example questions:
  - "You mentioned [X] — what's the main thing you'd want it to DO?"
  - "Who would use this, and in what situation would they trigger it?"
  After getting answers, proceed with the Medium format summary.

**⏸️ GATE-1 (MUST WAIT regardless of tier):** Do NOT proceed to Phase 2
until the user confirms or corrects. If corrections are received, update and
re-present until confirmed. The tier only controls WHAT you present, not
WHETHER you wait.

### Common Pitfalls

- Don't infer too much. If user said "PDF skill," don't assume OCR + merge + split
  unless explicitly stated. List what you know and ask.
- Domain matters more than you think. "Data skill" could mean analysis, cleaning,
  visualization, or ETL. Clarify early — wrong domain = wrong everything downstream.

---

## Phase 2: Research Existing Solutions

This phase prevents duplicating existing work. It is the most important
quality gate in the entire workflow.

**CRITICAL HARD STOP RULE (repeated from Anti-Cheat Rule #4):** If a Direct
Match is found in Step 2c, the workflow MUST stop completely and wait for
the user. Do NOT proceed to Phase 3 under ANY circumstance. This rule appears
at BOTH the phase header (here) AND the decision point (Step 2c) to prevent
any chance of bypassing.

### Step 2a: Search All Skill Registries

Search **FOUR sources in parallel** to maximize discovery. No single source
covers the entire skill ecosystem — combining them is essential.

---

**Source 1 — 🏪 SkillHub API (primary community registry):**

```bash
# On macOS/Linux:
curl -s --max-time 10 "https://lightmake.site/api/v1/search?q=<domain+keywords>&limit=10"
curl -s --max-time 10 "https://lightmake.site/api/v1/search?q=<task+keywords>&limit=10"

# On Windows PowerShell (curl is an alias for Invoke-WebRequest, use curl.exe):
curl.exe -s --max-time 10 "https://lightmake.site/api/v1/search?q=<domain+keywords>&limit=10"
curl.exe -s --max-time 10 "https://lightmake.site/api/v1/search?q=<task+keywords>&limit=10"
```

**Platform detection:** If `curl` fails with a PowerShell error message
(e.g., "Invoke-WebRequest : A parameter cannot be found"), the agent is
running on Windows — retry with `curl.exe` or skip directly to the
`web_fetch` fallback below.

**Timeout:** `--max-time 10` is mandatory. If either request times out or
returns non-JSON, treat that angle as failed and move on.

**Fallback (SkillHub only):**
1. `curl` unavailable/timed out → `web_fetch` `https://lightmake.site/api/v1/search?q=<keywords>&limit=10`
2. `web_fetch` fails → `web_search` `"site:lightmake.site <keywords>"`
3. All three fail → skip SkillHub; use remaining sources

For each result, record: `name`, `score`, `description` (one-line), and whether
it directly solves vs tangentially related.

---

**Source 2 — 📦 Vercel Skills CLI (open ecosystem):**

```bash
npx skills find <query>
```

Returns results like `vercel-labs/agent-skills@skill-name`.
Browse at https://skills.sh/

**If `npx skills` is not available** (command not found / Node.js missing),
skip this source silently and note in the report: "Vercel Skills CLI not
available — skipped."

For each result, extract: `package_name`, `description` (if available from
output), `source_url`.

---

**Source 3 — 🦞 ClawHub (OpenClaw registry):**

```bash
npx clawhub search <query>
```

Browse at https://clawhub.com/

**If `npx clawhub` is not available**, skip silently and note in report:
"ClawHub CLI not available — skipped."

For each result, extract: `slug`, `description` (if available).

---

**Source 4 — 🏠 Local Marketplace (pre-bundled skills):**

```bash
ls ~/.workbuddy/skills-marketplace/skills/ 2>/dev/null
```

If the directory exists, filter subdirectory names by keyword relevance
to the user's `core_task`. For each promising match, read the first ~30
lines of its `SKILL.md` for frontmatter classification.

**If the directory doesn't exist**, skip silently. This is a local cache
that may not be present on all installations.

---

**Merge & Deduplicate:**

After all sources complete (or fail gracefully), merge results:
1. Combine all results into a single list
2. Deduplicate by skill name/slug — if same skill appears in multiple sources,
   keep the entry with the most metadata (description, score)
3. Mark each result with its source icon for downstream classification
4. Proceed to Step 2b (local scan) and Step 2c (classification)

### Step 2b: Scan Local Installed Skills

Check these directories:

```
~/.workbuddy/skills/                                          # User-installed skills
~/.workbuddy/plugins/marketplaces/*/plugins/                  # Plugin suite top-level
~/.workbuddy/plugins/marketplaces/*/plugins/*/skills/         # Plugin suite sub-skills
~/.workbuddy/plugins/marketplaces/*/external_plugins/         # External marketplace skills
```

**Execution note:** ALL listed directories MUST be scanned. Plugin directories
often contain skills (e.g., finance-data-retrieval, pdf, xlsx) that users may
not think of as "their skills" but are fully functional. Missing a plugin skill
means missing a potential Bucket A/B match. If a directory doesn't exist, skip
it — but never skip a directory that does exist.

**How to scan:** List directory contents (e.g., `list_dir` or `ls`) to
discover subdirectories, then search for `SKILL.md` files recursively
(e.g., `search_file` with pattern, or `glob`). For each match, read the
first ~30 lines (e.g., `read_file` with line limit) for frontmatter and
initial body analysis — description pattern, trigger phrase coverage, and
resource structure. Do NOT skip this step or rely on memory — always scan
the actual filesystem.

**Scanning strategy for large directories:** Plugin marketplaces may contain
hundreds of skills. To scan efficiently:

1. Use `search_file` with pattern `SKILL.md`, `recursive: true`, and the
   target directory set to each marketplace path listed above
2. For each SKILL.md found, read only the first ~30 lines (frontmatter +
   initial body) — this contains the `description` and trigger phrases needed
   for classification
3. Do NOT read full SKILL.md bodies during scanning — only read the full file
   if the skill becomes a Bucket A/B candidate and needs detailed analysis

**Performance limit:** If a single scan returns more than 100 SKILL.md files,
prioritize scanning skills whose directory name or path contains keywords
related to the user's `core_task`. For example, if the user wants a "stock
analysis" skill, prioritize paths containing "finance", "stock", "trading",
"analysis" before scanning unrelated ones like "game-development".

### Step 2c: Classify Findings

Place each finding into exactly one bucket using these criteria:

**Source marking:** For each classified skill, note its source:
- 📦 **Installed** — from `~/.workbuddy/skills/` (user-created or manually installed)
- 🏪 **Marketplace** — from plugin marketplace directories (available to install)
- 🏠 **Local MSP** — from `~/.workbuddy/skills-marketplace/skills/` (pre-bundled, one copy away)
- 🦞 **ClawHub** — from ClawHub registry (`npx clawhub search`)
- 🏪 **SkillHub** — from SkillHub API (`lightmake.site`)
- 🛠️-hard **Built-in (Hard)** — deterministic system tools: `automation_update`, `deliver_attachments`, `preview_url`. Always available, no skill needed.
- 🛠️-soft **Built-in (Soft)** — AI-native capabilities: LLM writing/analysis, `web_search`/`web_fetch`, `image_gen`. Available but benefit from skill encapsulation for reliable output.

**Gap Fill vs Composite Check scope:**
- 🛠️-hard counts in BOTH Gap Fill AND Composite Check
- 🛠️-soft counts ONLY in Composite Check, NOT in Gap Fill
- Rationale: Gap Fill promises a concrete patch for a specific missing feature.
  "AI can write it" is not a patch — it's the AI doing what the AI does anyway.
  Composite Check evaluates whether the ecosystem can handle the combined task;
  AI-native capabilities are legitimate building blocks in that context.

#### 🔴 Bucket A: Direct Match (HARD STOP)

**Classification criteria (strict):**
- SkillHub score **> 0.3** AND the skill's description covers **≥80%** of the
  user's `core_task` keywords
- OR: An installed local skill has a description that fully encompasses all
  of the user's stated trigger scenarios

**Safety principle: When in doubt, classify as Direct Match.** It is always
better to show the user an option than to skip it silently. The cost of a
false positive here is low — the user sees an extra option and can dismiss it
in one message.

**Action:**

*Match-overlap assessment:* Compare the existing skill's description with the
user's `core_task` and `trigger_scenarios`. If the match covers ≥90% of the
user's stated functionality (near-identical feature set), use **High-Overlap
format**. Otherwise (70-89%), use **Standard format**.

*Multiple matches:* If multiple Bucket A matches exist (e.g., one from SkillHub
and one from local), present ALL of them in a single HARD STOP message. Let the
user decide which to try or whether to continue building — do NOT apply HARD
STOP multiple times.

**High-Overlap format (≥90% — near-duplicate):**
*Note: Gap Fill is NOT triggered for High-Overlap matches. The remaining <10%
gap is too small to justify a multi-source combination — the user should try
the existing skill as-is and evaluate if the minor gap matters in practice.*

> ⚠️ **Already exists:** `find-skills` — [one-line description]. It's already
> installed on your system. Want me to show you how to use it, or do you still
> want to build your own?
>
> - "Show me how to use it" / "看看怎么用" → load the existing skill into context, provide trigger phrases and usage instructions, then terminate the build workflow
> - "I still want to build my own" / "我还是想自己建" → continue to Phase 3

**Standard format (70-89% — partial overlap):**

> ⚠️ **I found an existing skill that covers much of what you need:**
>
> **[Skill Name]:** [one-line description]
> **Coverage:** ~[N]%. **Missing:** [specific gap, e.g., "auto-scheduling"]
>
> **Where to find it:** [installation path or link]
>
> 🔍 **Gap Fill check (mandatory):** Before presenting options, check whether
> the missing [N]% can be filled by other available sources:
> 1. List the specific sub-tasks NOT covered by this skill (the "gap")
> 2. Search Bucket B skills (📦/🏪) and 🛠️-hard built-in capabilities for each gap
>    (🛠️-soft like LLM writing or web_search do NOT count — Gap Fill requires
>    concrete, deterministic tools, not AI-native capabilities)
> 3. If ALL gaps can be filled → present **Gap-filled format** below
> 4. If SOME gaps cannot be filled → note which gaps remain in the presentation
>
> **If gap CAN be fully filled** (use this format instead of plain Standard):
>
> > ⚠️ **I found an existing skill that covers most of what you need:**
> >
> > **📦 `[Skill Name]`:** [one-line description]
> > **Base coverage:** ~[N]%. **Gaps filled by:**
> >
> > | Gap (missing feature) | Filled by | Source |
> > |----------------------|-----------|--------|
> > | [sub-task X] | `[skill-or-built-in]` | 🏪/📦/🛠️-hard |
> > | [sub-task Y] | `[built-in tool]` | 🛠️-hard |
> >
> > **Combined coverage:** ~100% (or actual % if gaps partially filled).
> > This means: use `[Skill Name]` as the main tool, supplement with the
> > above for the missing pieces.
> >
> > 📍 **Waiting for your decision.** Please respond with:
> > - **"Use it + fill gaps"** / **"用它+补丁"** → load the primary skill into context,
> >   provide usage instructions including how to supplement gaps, then terminate
> > - **"Try it first, see if gaps matter"** / **"先试试，看缺口是否真的需要"** → load the
> >   primary skill, let user evaluate if gaps are acceptable in practice
> > - **"Not quite right, here's why..."** / **"不太合适，因为..."** → record gaps and continue building
> > - **"I want to build my own anyway"** / **"我还是要自己建"** → Skip to Phase 3
>
> **If gap CANNOT be fully filled** (fall back to original Standard format):
>
> > ⚠️ **I found an existing skill that covers much of what you need:**
> >
> > **📦 `[Skill Name]`:** [one-line description]
> > **Coverage:** ~[N]%. **Remaining gap (~[M]%):** [what's missing, and why no filler found]
> >
> > 📍 **Waiting for your decision.** Please respond with:
> > - "Try this first" / "先试试这个" → load the existing skill into context, provide usage instructions, then terminate the build workflow
> > - "Not quite right, here's why..." / "不太合适，因为..." → I'll record the gaps and continue building
> > - "I want to build my own anyway" / "我还是要自己建" → Skip to Phase 3

**After presenting the match: STOP completely.** Do NOT begin any Phase 3
activities, do not prepare questions, do nothing but wait for user reply.

#### 💡 Bucket Composite: Multi-Skill Combination Check

After classifying all individual findings into Buckets A/B/C, check whether
MULTIPLE existing skills **and/or WorkBuddy built-in capabilities**, when
combined, could satisfy the user's needs without building a new skill from
scratch.

**Why this step exists:** The classification above evaluates each skill
one-to-one against the user's request. But users often describe compound
needs that no single skill covers, while 2+ existing skills together can
handle perfectly. Building a new monolithic skill in that case wastes time
and tokens — a thin "orchestrator" skill that chains existing skills is
faster and more maintainable.

**Built-in capabilities consideration:** WorkBuddy provides system-level
tools that are always available (no installation needed). These should be
factored into composite coverage evaluation alongside installed skills.

**🛠️-hard (deterministic system tools — count in BOTH Composite AND Gap Fill):**
- `automation_update` — scheduling recurring or one-time tasks
- `deliver_attachments` — delivering results/files to the user
- `preview_url` — previewing HTML/web content in-browser

**🛠️-soft (AI-native capabilities — count ONLY in Composite, NOT Gap Fill):**
- LLM writing/analysis — generating text content, reports, code, analysis
- `web_search` / `web_fetch` — web search and content retrieval
- `image_gen` — generating images from text descriptions
- `execute_command` — running system commands (general-purpose)

Do NOT list all built-in tools exhaustively — only include those that are
relevant to the user's specific sub-tasks.

**Why 🛠️-soft doesn't count in Gap Fill:** Gap Fill promises a concrete,
deterministic patch for a specific missing feature. "The AI can write that"
or "web_search can find that" is not a patch — it's the AI doing what it
does in every conversation. Gap Fill should only use 🛠️-hard tools that
provide a specific, repeatable mechanism (e.g., scheduled delivery, file
attachment). Composite Check, by contrast, evaluates whether the broader
ecosystem can handle a combined task — AI-native capabilities are legitimate
building blocks there.

**Execution order:** This step runs BEFORE the Bucket B high-coverage check
(below). Rationale: Composite matches present the most comprehensive
alternative first (2+ skills combined cover ≥80%). If the user rejects
the composite match, they can still see single-skill high-coverage
suggestions as a fallback. This ensures a progressive decision path:
most complete alternative → partial alternative → build from scratch.

**Transition rule:** If the user passes the composite check HARD STOP
(i.e., chooses "I still want a single unified skill" or "Gap is too big,
let's build"), and a high-coverage Bucket B item also exists, present
the Bucket B item as an FYI only — do NOT raise a WAIT. The user has
already made their decision. Example: "💡 You may also reference
`[skill-name]` for partial coverage of [specific sub-task]." No user
response is required; continue to Step 2d.

**When to trigger this check (ALL conditions must be met):**

1. No single Bucket A match exists (if one does, HARD STOP already active)
2. At least 2 coverage sources exist among: Bucket B skills (📦 installed
   or 🏪 marketplace) + relevant WorkBuddy built-in capabilities (🛠️)
3. The combined coverage of these sources covers ≥80% of the user's
   `core_task` sub-tasks

**How to evaluate:**

1. **Decompose the user's `core_task`** into discrete sub-tasks.
   Example: "extract tables from PDF and generate Excel reports" →
   sub-task A: extract tabular data from PDF |
   sub-task B: create formatted Excel workbook |
   sub-task C: generate summary report

2. **Map each coverage source to the sub-tasks it covers.** This includes
   Bucket B skills (📦 installed + 🏪 marketplace) AND relevant WorkBuddy
   built-in capabilities (🛠️-hard + 🛠️-soft — both count in composite
   because the evaluation is "can the ecosystem handle this?" not "is there
   a deterministic patch?"). Note which sub-tasks are covered by which
   source, and where overlap exists. Mark each source with its icon in the
   presentation table:
   - 📦 = already installed, ready to use
   - 🏪 = in plugin marketplace, one-click install
   - 🏠 = local marketplace, one copy away
   - 🦞 = ClawHub registry
   - 🛠️-hard = WorkBuddy deterministic tool, always available
   - 🛠️-soft = WorkBuddy AI-native capability, always available

3. **Check for strong complementarity:**
   - Sources cover DIFFERENT sub-tasks (minimal overlap) → strong composite
   - Sources cover the SAME sub-tasks (high overlap) → NOT a composite
     match (they compete, not complement)
   - Remaining uncovered sub-tasks are minor (<20% of total) → composite

**Quantitative coverage table (mandatory):** To reduce subjective bias in
composite coverage assessment, you MUST produce a structured table:

| Sub-task | 📦 `[skill-1]` | 🏪 `[skill-2]` | 🛠️-hard `[tool]` | 🛠️-soft | Uncovered |
|----------|:---:|:---:|:---:|:---:|
| [sub-task A] | ✅ | | | |
| [sub-task B] | ✅ | ✅ | | |
| [sub-task C] | | | ✅ | |
| [sub-task D] | | | | ❌ |

**Coverage calculation:** Count sub-tasks with at least one ✅. Divide by
total sub-tasks. If result ≥ 0.8, composite match condition 3 is met.
If < 0.8, no composite match — do NOT present the composite message.

**Overlap penalty:** If 2+ sources have ✅ on the same sub-task, count that
sub-task only once. Overlap does NOT increase coverage.

4. **If composite match found:** Present as HARD STOP (same weight as
   Bucket A — the user's need is already solvable without a monolithic build).

**Composite Match presentation format:**

> 💡 **Composite Match Found — you may not need a new skill from scratch:**
>
> These existing resources, when combined, cover about [N]% of what you described:
>
> | Source | Covers | How it helps |
> |--------|--------|--------------|
> | 📦 `[installed-skill]` | [sub-task A, B] | [specific capability] |
> | 🏪 `[marketplace-skill]` | [sub-task C] | [specific capability, requires install] |
> | 🛠️-hard `[built-in tool]` | [sub-task D] | [specific capability] |
> | 🛠️-soft | [sub-task E] | [AI-native: e.g., LLM writing, web_search] |
>
> (📦 = installed | 🏪 = marketplace | 🏠 = local MSP | 🦞 = ClawHub | 🛠️-hard = deterministic tool | 🛠️-soft = AI-native)
>
> **Combined coverage:** [summary of what's covered]. Remaining gap (if any):
> [specific uncovered sub-task, or "none — fully covered"].
>
> **Recommended approach:** Instead of building one large skill from scratch,
> I can build a thin **orchestrator skill** that chains these existing skills
> together. This is faster to create, uses fewer tokens at runtime, and stays
> maintainable as the underlying skills improve.
>
> 📍 **Waiting for your decision.** Please respond with:
> - **"Build orchestrator"** / **"构建编排器"** → Continue to Phase 3; Skill Brief will define
>   an orchestration skill that delegates to the existing skills
> - **"I still want a single unified skill"** / **"我还是要单体 skill"** → Continue to Phase 3 with the
>   original scope; existing skills recorded as heavy reference material
> - **"Show me how to use them together manually"** / **"教我手动组合使用"** → Provide combined usage
>   instructions (which skill to invoke first, how to pass output between them),
>   then terminate the build workflow

**⏸️ MUST WAIT for user response.** This is a HARD STOP — composite matches
are treated with the same weight as single Bucket A matches because the
user's need is already solvable without building a monolithic skill.

**If no composite match found** (skills overlap too much, or still leave
major gaps >20%):
- Do NOT present the composite match message
- Continue to Bucket B classification below

**Relationship to Bucket A Gap Fill:** Composite Check and Gap Fill serve
different but complementary purposes:

| Scenario | Mechanism | Coverage pattern | Example |
|----------|-----------|------------------|---------|
| One strong skill + small gap | Bucket A Gap Fill | 85% + 15% = 100% | `trading-analysis`(85%) + 🛠️-hard`automation_update`(15%) |
| No strong skill, but combo works | Composite Check | 35% + 35% + 30% = 100% | `browser-automation`(抓取) + `finance-data`(数据) + 🛠️-soft`web_search`(验证) + 🛠️-hard`deliver`(推送) |
| Both exist | Present BOTH, in order: Composite first, then Gap Fill | User sees best option first | |

**Execution order in Step 2c:**
1. First, classify all findings into Buckets A/B/C
2. If Bucket A match exists → run Gap Fill on it, present result (HARD STOP)
3. If NO Bucket A match (or user passes) → run Composite Check
4. If composite match exists → present composite options (HARD STOP)
5. If neither matches → proceed to Bucket B high-coverage check

This means Gap Fill runs BEFORE Composite Check when a Bucket A skill exists,
because a single-skill-plus-patch solution is simpler than a multi-skill orchestrator.
Only fall through to Composite if the user rejects the Gap Fill option.

**⚠️ Honesty constraint:** When in doubt about composite coverage, be
conservative — treat as "no composite match." Unlike Bucket A (where false
positives are cheap and false negatives waste time), a false positive here
could lead the user to depend on a combination that doesn't actually work.
This is especially important for 🏪/🦞 marketplace skills — you must verify
their SKILL.md description actually covers the claimed sub-task, not just
assume from the directory name or search result snippet. For 🏠 local
marketplace skills, read the actual SKILL.md frontmatter to confirm.
False negatives here are safer: the user builds a skill they could have
avoided, which is costly but not broken.

#### 🟡 Bucket B: Partial Match (Reference Material)

Skills that are related but don't fully solve the problem (score 0.1–0.3,
same domain but different task).

**High-coverage check:** If a Bucket B skill covers ≥60% of the user's
`core_task` (most of what they want is already doable with existing skills),
present a **coverage suggestion** BEFORE silently proceeding to build from
scratch:

> 💡 **Found skills that may cover most of what you need:**
>
> - `[skill-1]`: [what it covers]
> - `[skill-2]`: [what it covers, if multiple]
>
> These existing skills handle about [N]% of what you described. The remaining
> gap is: [specific gap].
>
> **Options:**
> - "Try combining existing skills first" / "先试试组合现有 skill" → load existing skills into context, provide combined usage instructions, then terminate the build workflow
> - "Gap is too big, let's build" / "缺口太大，还是建吧" → continue to Phase 3
> - "I want both — build + reference existing" / "两个都要——自建+参考现有" → continue to Phase 3, add
>   integration notes to Skill Brief

**⏸️ MUST WAIT for user response** before proceeding. Do NOT auto-proceed
to Phase 3 when high-coverage Bucket B skills exist.

**Standard coverage (< 60%):** Record as **Reference Materials** only. Extract
reusable patterns:
- Description trigger phrase style (what patterns do successful skills use?)
- SKILL.md section organization (workflow-first? reference-heavy?)
- Bundled resource choices (what goes in scripts/ vs references/?)
- Any quality techniques worth adopting

Continue to Step 2d.

#### 🟢 Bucket C: No Match

No relevant skills found (all scores < 0.1 or no results at all).

**Action:** Record "No existing solutions found." Note: this means Phase 3's
expert consultation becomes extra critical — there are fewer reference patterns
to learn from.

### Step 2d: Present Search Report

Compose a concise report:

```markdown
## Search Results

### Existing Solutions Found
[If Bucket A items exist → HARD STOP active, waiting...]
[If no Bucket A → "No direct match found"]

### Reference Materials (from Bucket B)

#### High Coverage (≥60%) — Already Presented Above
[Only include this block if a high-coverage Bucket B item exists AND the user
chose to continue building (i.e., they already saw the coverage suggestion
and decided to proceed). Summarize what was shown and what gap remains.]

#### Standard Coverage (<60%) — Patterns to Adopt
- **[skill name]**: [relevance] → Key pattern to adopt: [specific technique]
- ...
[If no standard-coverage Bucket B items, write "None found."]

### Gap Analysis
[REQUIRED for ALL scenarios — Bucket A/B/C — always include this section.]
- **Bucket A (direct match existed):** The existing skill covers [X] but lacks [Y].
  _If the user chooses to continue building (after HARD STOP), the new skill
  should differentiate by addressing: [specific gaps]_
- **Bucket B (partial match only):** Existing skills cover [N]% of the need.
  The remaining gap is: [specific gaps]
- **Bucket C (no match):** No reference patterns available. The Skill Brief in
  Phase 3 will rely more heavily on expert consultation; be conservative with
  assumptions.
[Use whichever bullet applies; remove the others.]
```

**After presenting:** If there WAS a Bucket A item, you're already waiting at
the hard stop — do NOTHING until user responds. If no Bucket A, auto-proceed
to Phase 3.

---

## Phase 3: Expert Consultation & Skill Brief

This phase transforms vague ideas into precise specifications. The output
is a **Skill Brief** — the single source of truth for Phase 4 generation.

### Why This Phase Matters

Creating a skill is like writing an API specification. The quality of the spec
determines the quality of the implementation. Skipping this = guessing =
a skill that half-works.

### Execution Strategy (Two Layers)

#### Layer 1: AI-Simulated Expert Consultation (DEFAULT)

This is the default mode. No user action required beyond confirming the result.

**How it works:** Using its own domain knowledge combined with the Reference
Materials from Phase 2, the AI acts as the domain expert itself. It generates
a comprehensive Skill Brief by asking itself (and optionally the user) targeted
questions about trigger precision, workflow depth, domain secrets, resource needs,
and failure modes.

**Advantages:**
- Fully automatic — no UI switching, no external dependencies
- Fast — produces results in one message exchange
- Quality baseline established — better than raw user input alone; however,
  for highly specialized or niche domains, user review of the Brief is critical

**Process:**

1. Review Intent Summary (P1) + Reference Materials (P2)
2. Synthesize into a draft Skill Brief covering all fields. Include
   `suggested_extensions` if the domain analysis reveals capabilities that
   commonly accompany the user's stated core_task but were not mentioned.
   Base these on: (a) patterns from Phase 2 reference skills, (b) domain
   expertise about typical workflows in this area. Limit to 2-3 suggestions
   with concise rationale. If no relevant extensions exist, set to "None —
   the current scope covers typical needs in this domain."
3. **Self-assessment checklist** — for each item, mark ✅ or ❌ and explain gaps:
   - `trigger_phrases` includes at least 3 colloquial/informal phrases (not just formal terms)?
   - `core_workflow` references at least one specific tool or API from Phase 2 reference skills?
   - `domain_knowledge` contains terminology NOT found in general AI training data?
   - `edge_cases` includes at least one scenario derived from Phase 2 reference skill limitations?
   If any ❌, explicitly note the gap and flag: "Consider upgrading to Layer 2
   for [specific area]." If Bucket C (no reference skills found), add: "No
   reference skills found. Layer 2 is strongly recommended for specialized domains."
4. If the user's original request was vague (`confidence: low`), ask 2-3
   clarifying questions before finalizing (note: GATE-1 in Phase 1 may have
   already asked some — check P1 history to avoid repeating questions)
5. Present the complete Skill Brief to the user
6. Wait for approval (GATE-3c)

#### Layer 2: External Expert Consultation (OPTIONAL UPGRADE)

Only triggered when:
- User explicitly requests ("I want to ask a real expert")
- Layer 1 produced results the user deems insufficient
- The domain is highly specialized and the AI's general knowledge may be inadequate

**Mode A — Expert Center:**

Provide the user with a ready-to-paste brief:

> Please help me design a WorkBuddy skill with these specifications:
>
> **Domain:** [from P1]
> **Core Task:** [from P1]
> **Trigger Scenarios:** [from P1]
> **Reference Skills Found:** [summary from P2]
> **Gaps to Fill:** [from P2 gap analysis]
>
> Please provide:
> 1. Refined one-paragraph purpose statement
> 2. 8-15 trigger phrases (English and Chinese)
> 3. Key domain terminology to include
> 4. Core workflow steps (step-by-step)
> 5. Recommended bundled resources
> 6. Edge cases or pitfalls

Wait for the user to return expert results. If the pasted output is incomplete
or unclear, do NOT guess missing parts — ask clarification before compiling.
Then merge into Skill Brief.

**Mode B — Guided Interview:**

Ask 2-3 targeted questions per round (avoid overwhelming):
- Trigger precision: Beyond "[main trigger]", how else might users ask?
- Workflow depth: Walk through ideal usage step-by-step
- Domain secrets: What does an expert know that a general AI wouldn't?
- Resource needs: Tools, APIs, templates, reference docs?
- Failure modes: What commonly goes wrong?

Compile answers into the Skill Brief template.

**⏸️ GATE-3a (Layer 2 ONLY):** Only wait for mode selection if the user opts
into Layer 2. For Layer 1 (default), skip GATE-3a entirely.

### Skill Brief Template

All modes must produce this structured output:

```yaml
skill_name:           # kebab-case identifier
skill_type:           # "standard" | "orchestrator". Default: "standard".
                      # Set to "orchestrator" if user chose "Build orchestrator"
                      # in Phase 2 composite check. An orchestrator skill delegates
                      # to existing skills rather than implementing functionality.
purpose:              # One paragraph: what & why
trigger_scenarios:    # From Phase 1: in what situations would the user invoke this?
                      # (feeds directly into description's "when to trigger" language)
trigger_phrases_en:   # 8-15 English natural-language phrases
trigger_phrases_zh:   # 8-15 Chinese natural-language phrases
core_workflow:        # Step-by-step: what the skill should DO
domain_knowledge:     # Key concepts, terminology, heuristics
bundled_resources:    # scripts/, references/, assets/ plan
suggested_extensions: # Optional: related capabilities the user may not
                       # have considered but complement the core task.
                       # Based on P2 reference skill patterns + P3 domain
                       # expertise. Max 3 items. "None" is valid.
                       # Format: "- [capability]: [why it fits]"
edge_cases:           # Known tricky scenarios to handle
expert_notes:         # Additional guidance from consultation (if any)
```

**Field mapping for Phase 4:**
- `trigger_scenarios` → feeds into description's "when to trigger" language
  and the SKILL.md body "When to Use" section
- `trigger_phrases_en/zh` → embedded directly into description frontmatter
- `purpose` → starting point for the SKILL.md body "Purpose" section

### ⏸️ GATE-3c: Approval (ALL Layers)

Present the complete Skill Brief to the user:

```markdown
## Skill Brief — Ready for Generation

**Name:** `[skill_name]`
**Purpose:** [paragraph]

**Trigger Phrases (EN):** [... all phrases ...]
**Trigger Phrases (ZH):** [... all phrases ...]

**Core Workflow:**
1. [step]
2. [step]
...

**Bundled Resources:** [plan]
**Edge Cases:** [list]

**Suggested Extensions (optional):** [list of extensions with rationale,
or "None — the current scope covers typical needs in this domain."]
```

Ask: "Does this look right? Any changes before I generate the skill?"

**MUST WAIT** for user approval or edits. This is the last chance to adjust
before code generation. If edits requested, update and re-present.

**Transition:** After GATE-3c approval, output a brief status message before
beginning Phase 4: "✅ Brief approved. Initializing skill files — this may
take a moment..."

---

## Phase 4: Generate the Skill

**Scope note:** This skill orchestrates the WHEN (when to search, when to consult,
when to generate). For detailed SKILL.md mechanics (frontmatter format, progressive
disclosure, example patterns, specific writing conventions), defer to
`skill-creator`'s built-in guidance once loaded. The rules below are **minimum
quality standards**, not replacements for skill-creator's full methodology.

Load the `skill-creator` skill for framework guidance, then execute:

### Step 4a: Initialize

```bash
python <skill-creator-path>/scripts/init_skill.py <skill-name>
```

**Scope decision:** Default to user scope (`~/.workbuddy/skills/`). Use project
scope (`.workbuddy/skills/`) only if user explicitly requests team sharing.

**⚠️ Name collision handling (Edge Case #8):** If `init_skill.py` reports the
directory already exists, ask the user: overwrite, rename, or cancel. Do NOT
silently overwrite existing skills.

### Step 4b: Write SKILL.md

Follow these minimum quality standards strictly:

**Frontmatter (Metadata):**

```yaml
---
name: <kebab-case name>
description: >
  Third-person imperative form. 100-150 words. Must include 10-20 specific
  trigger phrases (bilingual EN + ZH). Format:
  "This skill should be used when the user wants to [phrase 1], [phrase 2], ...
  Trigger phrases include: [more phrases]."
description_zh: >
  Chinese translation with Chinese trigger phrases.
_localized_zh: "true"  # Include if providing zh translation
---
```

**Description quality determines trigger accuracy.** Critical rules:
- Use third-person imperative ("This skill should be used when...")
- Include colloquial/natural phrases users actually say, not just formal terms
- Cover both English and Chinese triggers for bilingual accessibility
- Focus on WHEN to trigger (scenarios), not just WHAT it does
- Test mentally: "Would a user naturally say [phrase] and have this match?"
**Description writing process:** Before writing the description body:
1. List 20 candidate trigger phrases (EN + ZH), mixing formal, colloquial,
   and action-oriented phrasing
2. Filter to 10-20 most representative phrases
3. Ensure coverage of: formal terms, casual speech, compound phrases, pinyin variants
4. Embed all selected phrases into a flowing paragraph in third-person imperative form

**Body length:** Minimum 1000 words. Recommended 1500-2000 words for skills
with complex workflows or rich domain knowledge. If content exceeds 2000 words,
move detailed reference material to `references/` files — link to them from the
body. The 1000-word floor is a hard minimum — below this, the skill likely
lacks sufficient workflow detail.

**Word count estimation:** Do NOT count words precisely — LLM word counters are
unreliable. Instead, verify section completeness:
- [ ] Purpose (1-2 sentences that clearly state what and why)
- [ ] When to Use (4+ bullet trigger scenarios covering formal, colloquial, and
      compound phrases; bilingual EN + ZH)
- [ ] Workflow (3+ actionable steps, each with specific tool or API references)
- [ ] Key Concepts / Domain Knowledge (3+ terms or heuristics, each with a brief
      definition or decision rule)
- [ ] Bundled Resources (each listed file has a stated purpose; no orphaned or
      unexplained entries)
- [ ] Edge Cases & Troubleshooting (2+ scenarios, each with a recovery or
      resolution step)

If all 6 sections meet these minimums, the word count target is likely met.

**Body structure — write in this order:**

1. **Purpose** (1-2 sentences)
2. **When to Use** (bullet list of trigger scenarios)
3. **Workflow** (step-by-step execution guide with tool references)
4. **Key Concepts / Domain Knowledge** (terminology, heuristics, best practices)
5. **Bundled Resources** (what each file does, when to use it)
6. **Edge Cases & Troubleshooting** (known pitfalls and recovery steps)

**Writing style:** Imperative/infinitive form throughout. No second person.
Example: "To create the skill, run init_skill.py" — NOT "You should run..."

### Orchestrator Skill Differences

When `skill_type: orchestrator`, the SKILL.md differs from standard skills:

**Frontmatter description:** Emphasize coordination, not implementation.
Use phrases like "orchestrates X and Y," "chains skill A with skill B,"
"coordinates the workflow between..." Do NOT describe the delegated
skills' capabilities as if this skill implements them.

**Body Workflow section:** Describe the orchestration logic:
1. Which skill to invoke first and with what input
2. How to pass output from skill-1 to skill-2 (format, transformations)
3. Error handling if a delegated skill fails or is unavailable

**Bundled Resources:** Instead of implementing scripts, provide an
orchestration config file (e.g., `references/orchestration.md`) that
documents the call sequence, input/output mappings, and fallback behavior.
Keep scripts/ minimal — the heavy lifting is done by delegated skills.

**⚠️ Anti-pattern warning:** Do NOT copy implementation details from
delegated skills into the orchestrator's body. If the orchestrator can
function without a delegated skill being loaded, it is not an orchestrator —
it is a standard skill with references. The orchestrator's body should
read as a thin coordination layer, not a duplicate of existing documentation.

### Step 4c: Create Bundled Resources

Create resources identified in the Skill Brief:

- **scripts/**: Executable code for deterministic/repeated operations
- **references/**: Documentation loaded on-demand (keeps body lean)
- **assets/**: Templates, images, or files used in final output

**Clean up:** Delete ALL unused example files generated by `init_skill.py`.

### ⏸️ GATE-4: Pre-Package Review

Before running `package_skill.py`, present the user with a quality checklist:

```markdown
## Pre-Package Review

✅ **SKILL.md body length:** [N] words (minimum 1000; recommended 1500+; verify by section completeness, not word counting)
✅ **Frontmatter complete:** name ✓ description ✓ [description_zh] ✓
✅ **Trigger phrases:** [N] total ([N_EN] EN + [N_ZH] ZH)
✅ **Bundled resources:** [list or "none"]
✅ **Example files cleaned:** [yes/no]
```

**MUST WAIT for user confirmation ("looks good" / "fix X") before packaging.**
This is the last opportunity to catch issues before the .zip is created.

If edits requested, implement them and re-present. Once approved, proceed to 4d.

### Step 4d: Validate and Package

```bash
python <skill-creator-path>/scripts/package_skill.py <path/to/skill-folder>
```

**Windows fallback:** If `package_skill.py` fails due to GBK encoding, use
inline Python with zipfile:

```python
python -c "
import zipfile, os
d = r'<path-to-skill-folder>'
z = r'<output-path>.zip'
zf = zipfile.ZipFile(z, 'w', zipfile.ZIP_DEFLATED)
for r, _, fs in os.walk(d):
    for f in fs:
        zf.write(os.path.join(r, f), os.path.relpath(os.path.join(r, f), d))
zf.close()
print('OK:', z)
"
```

**If validation fails:**
1. Read error messages carefully
2. Common fixes: missing frontmatter fields, short/vague description,
   invalid kebab-case naming, orphaned file references, example files not cleaned
3. Fix and retry (**max 3 validation attempts** — independent counter from
   Phase 5 iteration counter)

**Circuit breaker:** If all 3 validation attempts fail, do NOT retry further.
Instead, present the raw SKILL.md content to the user with a summary of
recurring validation errors, and ask for manual intervention: "I've attempted
to fix the validation errors 3 times without success. Here are the persistent
issues: [summary]. Would you like to manually edit the file, or should we
revise the Skill Brief (return to Phase 3)?"

**If validation passes:** Note the output `.zip` file path. Proceed to Phase 5.

---

## Phase 5: Deliver and Test

### Step 5a: Present Result

Show the user:

```markdown
## ✅ Skill Created: `[skill-name]`

**Package:** `<path-to-skill-name.zip>`

**Contents:**
- `SKILL.md` ([N] words, [N] sections)
- `scripts/` ([N] files: [list])
- `references/` ([N] files: [list])
- `assets/` ([N] files: [list])
```

### Step 5b: Installation

**Option 1 — Install now:**
Extract `.zip` to `~/.workbuddy/skills/skill-name/`

**Option 2 — Manual:**
User extracts themselves; provide instructions.

### Step 5c: Test Prompts

Suggest concrete verification prompts based on the skill's trigger phrases:

> ### Recommended Testing
>
> **Positive tests (should trigger the skill):**
> 1. "[trigger phrase 1]"
> 2. "[trigger phrase 2]"
> 3. "[trigger phrase 3]"
>
> **Negative test (should NOT trigger this skill):**
> 4. "[unrelated phrase that shouldn't match]"
>
> After testing, let me know:
> - 🔵 Works perfectly — you're done!
> - 🟡 Mostly works, but... — tell me what's off and I'll iterate
> - 🔴 Doesn't trigger — I'll debug the description

### Step 5d: Iterate if Needed

If user reports issues after testing:

1. **Diagnose root cause:**
   - Not triggering? → Most likely description lacks specific phrases → audit and expand
   - Wrong behavior? → Workflow instructions need fixing → edit SKILL.md body
   - Missing features? → Add steps or bundled resources
2. **Implement fix** in the appropriate location
3. **Re-package** via Step 4d
4. **Re-present** updated package

**Escalation rule:** If **3+ iteration rounds** fail to resolve the issue,
suggest returning to Phase 3 for Skill Brief revision — fundamental misalignment
likely occurred during requirements gathering. Note: iteration counter is
INDEPENDENT from validation retry counter (Step 4d has its own max-3 limit).

---

## Edge Cases

| # | Scenario | Recovery Action |
|---|----------|-----------------|
| 1 | **All network sources unreachable (SkillHub, Vercel, ClawHub)** | Continue with local scan (Step 2b) + local marketplace (Source 4) only; note limitation in Search Report. If a network source returns non-JSON response (e.g., HTML error page), treat as unreachable for that source only — other network sources may still work. |
| 2 | **All network AND local sources fail** | Proceed with local scan only; accept reduced coverage; Phase 3 should flag "no reference patterns found" |
| 3 | **User refuses consultation (Layer 1+2)** | Use P1+P2 data alone for best-effort Skill Brief; warn result may be suboptimal |
| 4 | **User abandons mid-flow** | If conversation context is still available, resume from the last completed artifact (Intent Summary / Search Report / Skill Brief) and re-state which GATE was last passed. If context is lost, advise the user to restate their original request to restart. |
| 5 | **Validation fails repeatedly (Step 4d)** | Read errors, fix, retry (max 3 attempts); common issues listed in Step 4d |
| 6 | **Generated skill doesn't trigger** | Audit description against user's actual wording; expand with colloquial alternatives |
| 7 | **User wants to skip phases** | Acknowledge but warn about quality risks; proceed with available data |
| 8 | **Skill name collision (Step 4a)** | Ask user: overwrite, rename, or cancel. Never silent-overwrite |
| 9 | **Incomplete expert output (Layer 2 Mode A)** | If user pastes partial expert response, do NOT guess missing parts. Ask clarification before compiling Brief |
| 10 | **User request violates safety/security rules** | If the requested `core_task` involves malicious actions, bypassing security controls, or violates platform content policy, immediately halt and explain: "This request cannot be fulfilled as it conflicts with safety guidelines. Please adjust your requirements." Do NOT attempt to build a workaround. |
| 11 | **User changes requirements after GATE approval** | If change occurs before Phase 4 starts, return to the most recent GATE and re-confirm. If change occurs during/after Phase 4, complete the current iteration first, then handle the change as a Phase 5d iteration (not a full workflow restart). |

---

## Workflow Diagram (Quick Reference)

```
User: "创建一个 skill 做 XXX"
         │
         ▼
┌─────────────────────┐
│ Phase 1: Analyze     │ ← Extract domain, task, triggers, confidence
│         Output:     │   Intent Summary
│   Intent Summary    │
└────────┬────────────┘
         │ ⏸️ GATE-1: Confirm (tiered by user_confidence)
         ▼ (confirmed)
┌─────────────────────┐
│ Phase 2: Research   │ ← 4-source parallel search + local scan
│         Output:     │   Classification (🔴/🟡/🟢) + References
│   Search Report     │
└────────┬────────────┘
         ├─ 🔴 Direct Match → 🛑 HARD STOP: "Try this first?"
         │   └─ (user says "not good enough") ──────────────────┐
         │                                                       │
         ├─ 💡 Composite Match (2+ skills ≥80%)                 │
         │   → 🛑 HARD STOP: "Build orchestrator?"              │
         │   └─ (user chooses orchestrator / unified) ──────────┤
         │                                                       │
         ├─ 🟡 Partial Match, high-coverage (≥60%)              │
         │   → ⏸️ WAIT (FYI only if composite already passed)   │
         │   └─ (user says "gap too big / both") ───────────────┤
         │                                                       │
         └─ 🟡 Partial Match, standard (<60%) / 🟢 No match ───┘
                     │
                     ▼
┌─────────────────────┐
│ Phase 3: Consult    │ ← Layer 1: AI simulates (DEFAULT, auto)
│         OR          │   Layer 2: External expert (optional upgrade)
│         Output:     │   Skill Brief (THE spec document)
│   Skill Brief       │
└────────┬────────────┘
         │ ⏸️ GATE-3c: Approve brief (last chance before code gen)
         ▼ (approved)
┌─────────────────────┐
│ Phase 4: Generate   │ ← Load skill-creator → Init → Write → Review
│         Output:     │   Complete skill directory
│   Skill Files       │
└────────┬────────────┘
         │ ⏸️ GATE-4: Pre-package review
         ▼ (approved)
┌─────────────────────┐
│ Phase 5: Deliver    │ ← Present .zip → Install → Test → Iterate
│         Output:     │   Installed + tested skill
│   Ready-to-use      │
└─────────────────────┘
```

---

## Quality Checklist (Self-Check Before GATE-4)

_This checklist is for AI self-check before presenting GATE-4. The GATE-4
review presented to the user should use the Phase 4 format. Do NOT present
both checklists to the user._

Before presenting the pre-package review (GATE-4), verify every item:

- [ ] **Description uses third-person imperative** ("This skill should be used when...")
- [ ] **Description contains 10-20 trigger phrases** (bilingual EN + ZH, include colloquial)
- [ ] **SKILL.md body meets minimum length** (1000+ words; verify by section completeness)
- [ ] **All workflow steps are actionable** (verb-first, specific tool references)
- [ ] **Bundled resources justified** (each file has clear purpose)
- [ ] **No orphaned example files** remain from init
- [ ] **Edge cases section exists** (even if brief)
- [ ] **Skill Brief fully consumed** (nothing from P3 ignored)
- [ ] **Frontmatter valid** (name, description, optional description_zh + _localized_zh)

---

## Quick Reference Summary

_Note on GATE numbering: GATE numbers reflect semantic function, not sequential
order. Conditional GATEs (e.g., 3a for Layer 2 only) and terminal exits (Phase 2
HARD STOPs) are intentionally non-sequential or unnumbered to signal their
distinct role._

| Phase | Primary Action | Gate(s) | Auto or Wait? |
|-------|---------------|---------|---------------|
| 1. Analyze | Extract domain, task, triggers, confidence | GATE-1: Confirm (tiered by confidence) | ⏸️ Wait |
| 2. Research | 4-source parallel (SkillHub + Vercel + ClawHub + local MSP) + local scan → classify → composite check | HARD STOP on 🔴 or 💡 composite; ⏸️ wait on 🟡 high-coverage (>60%) | ⏸️ Wait if 🔴/💡/🟡 high-coverage; else auto |
| 3. Consult | Layer 1: AI auto-generates Brief (default); Layer 2: External expert (optional) | GATE-3c: Approve Brief | 🔄 Auto (L1); ⏸️ Wait ×2 (L2+approval) |
| 4. Generate | Init → write SKILL.md → create resources | GATE-4: Pre-package review | ⏸️ Wait |
| 5. Deliver | Present .zip → install → test prompts → iterate | None (iteration loop) | 🔄 Auto (iterate up to 3×) |

**Retry counters:** Step 4d validation retries and Step 5d iteration retries are
INDEPENDENT — each has its own max-3 limit.

**Counter reset rule:** Any action that returns the workflow to Phase 3
(i.e., revising the Skill Brief) automatically resets BOTH the Step 4d
validation counter AND the Phase 5d iteration counter to 0. Edits within
the same phase (e.g., fixing a typo in Phase 4) do NOT reset counters.
