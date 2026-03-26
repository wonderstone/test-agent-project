# Architecture

This document describes the structure and design of the Agent Framework Template — a reusable, drop-in framework for structuring AI coding agents (GitHub Copilot, Cursor, Augment Code, Windsurf, etc.).

---

## What This Repository Is

This repository is a **documentation-only framework template**. It contains no runnable source code. Its purpose is to be copied into any software project so that AI coding assistants in that project receive:

- Structured, consistent decision rules
- Cross-session state management
- A clear operating protocol with layered instruction loading

---

## Repository Layout

```
.github/
  copilot-instructions.md          ← Layer 1: operating rules (10 rules, always loaded)
  project-context.instructions.md  ← Layer 2: project adapter (fill in per project)
  agents/
    architect.agent.md             ← analysis / planning / critique agent definition
    implementer.agent.md           ← execution / validation / change agent definition
  instructions/
    backend.instructions.md        ← protocol loaded for backend code changes
    docs.instructions.md           ← protocol loaded for documentation changes

docs/
  INDEX.md                         ← navigation index for all TYPE-A documents
  FRAMEWORK_ARCHITECTURE.md        ← deep explanation of the layer system
  ADOPTION_GUIDE.md                ← step-by-step setup for a new project

templates/
  project-context.template.md      ← blank project adapter to customize
  session_state.template.md        ← blank cross-session state file

ARCHITECTURE.md                    ← this file
README.md                          ← quick-start and compatibility overview
ROADMAP.md                         ← phase planning and milestone tracking
```

---

## The 4-Layer Instruction System

The framework separates concerns into four layers, each with a distinct load condition:

| Layer | File | Load condition | Contains |
|---|---|---|---|
| 1 — Operating rules | `copilot-instructions.md` | Always | HOW the agent behaves |
| 2 — Project adapter | `project-context.instructions.md` | Multi-step task start or keyword match | WHERE to find project facts |
| 3 — Canonical docs | `docs/*.md` files | Topic confirmed relevant | WHAT the project's architecture is |
| 4 — Code files | Source files to be edited | Immediately before any edit | The actual current state of the code |

**Conflict resolution**: Layer 4 (code) overrides Layer 3 (docs), which overrides Layer 2 (adapter).

---

## Core Components

### Layer 1 — Operating Rules (`copilot-instructions.md`)

The always-loaded behavior specification. Contains 10 mandatory rules:

| Rule | Name | Summary |
|---|---|---|
| 0 | Challenge Incorrect Statements | Validate user claims against code; never accept incorrect descriptions silently |
| 1 | Dangerous Operations | Check protected paths before any delete, overwrite, or replace |
| 2 | Read Before Act | Read the actual file before editing; never rely on memory of a prior session |
| 3 | Critical Topic Triggers | Keywords trigger loading the project adapter automatically |
| 4 | Validate After Every Change | Lint / type-check / test after every change before moving on |
| 5 | Dispatch Decision Disclosure | Disclose fan-out vs. serial decision before any multi-step task |
| 6 | Document Organization | Classify docs as TYPE-A / TYPE-B / TYPE-C and place them accordingly |
| 7 | Cross-Session State | Maintain `session_state.md` for goal, active work, decisions, and insights |
| 8 | Reply Footer | End every reply with a status line (focus / now / next) |
| 9 | Subtask Completion Checkpoint | Update ROADMAP, criteria, state, and footer atomically on subtask done |
| 10 | Phase Graduation Protocol | Archive, promote insights, rotate state, and mark ROADMAP on phase complete |

### Layer 2 — Project Adapter (`project-context.instructions.md`)

A per-project configuration file that tells the agent WHERE things are. It is a navigation index, not a reference document. It contains:

- **Project map** — directory → purpose mapping
- **Critical topic triggers** — keyword → canonical doc routing
- **Protected paths** — files that require explicit confirmation before destructive operations
- **Build and test commands** — exact commands to validate changes
- **Runtime config locations** — where environment and config files live

### Layer 3 — Canonical Docs (`docs/`)

Long-lived TYPE-A documentation files indexed in `docs/INDEX.md`. Loaded only when a task actually touches the relevant topic. Examples in this template:

- `docs/FRAMEWORK_ARCHITECTURE.md` — deep explanation of the layer design
- `docs/ADOPTION_GUIDE.md` — step-by-step setup guide

### Layer 4 — Code Files

The actual source files being edited. The agent must read the current state of a file immediately before making any change. The current file state is always authoritative.

---

## Two-Agent Model

The framework supports a two-agent architecture for larger tasks:

| Agent | File | Responsibility |
|---|---|---|
| **Architect** | `.github/agents/architect.agent.md` | Analysis, planning, and critique. Produces checklists and acceptance criteria. Does NOT write implementation code. |
| **Implementer** | `.github/agents/implementer.agent.md` | Execution and validation. Follows the architect's checklist. Makes minimal changes and validates immediately. |

Both agents are optional. For simple tasks, a single agent operates in both modes.

---

## State Model

Cross-session state is tracked in `session_state.md` at the project root.

```
session_state.md
  Current Goal         — one sentence; survives phase transitions
  Active Work          — what is in progress right now
  Completed This Phase — verified subtasks; cleared on phase graduation
  Blocked / Pending    — waiting on external input
  Acceptance Criteria  — observable conditions marking phase complete
  Phase Decisions      — key choices made this phase with rationale
  Technical Insights   — durable patterns/traps; never auto-deleted
```

When `session_state.md` exceeds ~100 lines, old phase content is archived to `docs/archive/`.

---

## Document Type System

| Type | Definition | Location | In INDEX.md? |
|---|---|---|---|
| TYPE-A | Long-lived: architecture, runbooks, API specs, guides | `docs/` or module root | Yes |
| TYPE-B | Module-local, evolves with code | Module directory | No |
| TYPE-C | Phase reports, one-time analyses, summaries | `docs/archive/` | No |

---

## Adoption

This framework is designed to be **copied into another project** and customized. See `docs/ADOPTION_GUIDE.md` for the full setup walkthrough, or `README.md` for the minimal three-command setup.
