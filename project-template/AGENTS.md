# Device Pipeline Agent Guide

> **AI authorship:** This file was written by Codex, an AI coding agent from OpenAI.

## What this workspace is

This is a per-device workspace for running the pipeline defined in `llm-device/`.

The user may say only: “Do the next step in the pipeline.” When that happens, determine the next incomplete stage, read its instructions, execute it, verify it, and create its numbered output folder. Do not merely describe what should be done.

## Never modify `llm-device`

`llm-device/` is the pipeline source repository and is read-only during pipeline runs.

If `llm-device/` does not exist yet, run this exact command from the per-device workspace root:

```bash
git clone git@github.com:Genesis-AmSC-FES/llm-device.git
```

Use the user's configured SSH key through the normal Git/SSH setup. After cloning, verify that `llm-device/` exists and that `git -C llm-device status --porcelain` is clean, then apply the read-only rules below. If cloning fails because of SSH authentication, network access, or repository permissions, report that narrow blocker; do not silently substitute another repository, protocol, or source.

- Do not edit, create, delete, rename, or generate anything inside it.
- Do not run Git operations that change it.
- Do not put outputs, downloads, environments, caches, or temporary files there.
- Reading its instructions, design files, README, and Git status is allowed.
- At the end of each stage, confirm `git -C llm-device status --porcelain` is clean.

Only a later user instruction that explicitly says to modify `llm-device` can override this rule. “Do the next step” does not override it.

Put all device-specific work in numbered folders beside `llm-device/`.

## Pipeline stages

Before running a stage, read its complete instruction file in `llm-device/Drafts/`.

| Stage | Output folder | Instruction file |
|---:|---|---|
| 1 | `01 - Find Official Product Pages` | `Find Official Product Pages - Instructions.md` |
| 2 | `02 - Download Official Product Guides` | `Download Official Guides - Instructions.md` |
| 3 | `03 - Enumerate Interfaces` | `Enumerate Interfaces - Instructions.md` |
| 4 | `04 - Set Goals and Interfacing` | `Set Goals and Interfacing - Instructions.md` |
| 5 | `05 - Select Methods and Settings` | `Select Methods and Settings - Instructions.md` |
| 6 | `06 - Hello Planning` | `Hello Planning - Instructions.md` |
| 7 | `07 - Check for Drivers and Core Libraries` | `Check for Drivers and Core Libraries - Instructions.md` |
| 8 | `08 - Install Drivers and Core Libraries` | `Install Drivers and Core Libraries - Instructions.md` |
| 9 | `09 - Physically Connect` | `Physically Connect - Instructions.md` |

The broader roadmap after these stages is in `llm-device/Design/Workflow Sketch.xlsx`. Not every later task has its own instruction file.

The stage-specific instruction is authoritative if it is more detailed or restrictive than this guide.

## How to determine the next step

1. Inspect the workspace root and existing numbered folders without changing them.
2. Check whether each stage has its required, valid artifacts. A folder name alone does not prove completion.
3. Choose the first incomplete stage in pipeline order.
4. Read that stage’s entire instruction file.
5. Read its required upstream artifacts, starting with JSON summaries when available.
6. Execute the stage and create or finish its numbered output folder.

If the workspace has no numbered folders, start with Stage 1. Use the manufacturer and exact model from the user’s message. If they are missing, ask only for the minimum identity information Stage 1 requires.

If an earlier stage is partial, blocked, invalid, or missing a required handoff, do not silently skip it. Resolve it within scope or report the narrow blocker.

Do not redo completed stages merely to derive their conclusions again. Use their outputs as upstream handoffs.

## Output rules

- Create outputs at the workspace root, never inside `llm-device/`.
- Use the numbered folder names in the table above.
- Follow the filenames, schemas, and chat-output contract in the active stage instructions.
- Preserve prior-stage files unless the user explicitly requests a correction.
- Keep Markdown and machine-readable outputs consistent.
- Validate every JSON file with `jq empty`.
- Validate other structured outputs with an appropriate local tool when available.
- Use `null`, empty arrays, or explicit partial/blocked states instead of inventing missing facts.
- Keep detailed evidence in files; keep the final chat response concise and link the created artifacts.

### AI-authorship notice

Every artifact created or materially rewritten by an AI agent must identify itself as AI-generated and name the agent that produced it. Use the agent's actual available identity; do not guess a model or version. If the exact model is unavailable, the product/agent and provider are sufficient, for example: `Codex (OpenAI)`.

Keep the notice compatible with the file format:

- Markdown or plain text: put a short visible notice immediately below the title or at the top.
- JSON: add a top-level `_ai_generation` object containing at least `ai_generated: true`, `agent`, and `provider`.
- CSV: add `ai_generated_by` and `ai_provider` columns.
- Source code, scripts, and configuration files that support comments: add a comment header.
- Other structured formats: use a native metadata field or comment without making the file invalid.
- Binary formats that cannot safely carry visible metadata: create a same-name `.metadata.json` sidecar with the authorship fields.

Do not add an AI-authorship notice to official manuals, manufacturer downloads, user-supplied files, or other files merely copied unchanged. Preserve those originals exactly and record their provenance in the AI-generated descriptor or manifest instead.

## Evidence rules

- Ground device facts in official manufacturer sources or upstream artifacts derived from them.
- Prefer the most exact model, variant, interface, operating system, and document revision.
- Do not silently treat forums, resellers, blogs, mirrors, or generic recollection as authoritative.
- Follow each stage’s web-search limits. Stage 2, in particular, is restricted to the official manufacturer site supplied by Stage 1.
- Clearly distinguish official facts, terminal observations, user observations, assumptions, and engineering judgment.
- If evidence is incomplete, produce an honest partial result or identify the exact missing input.

## Conversation and hardware rules

- Stage 4 requires a focused conversation about the scientist’s goals and explicit acceptance of the interface decision before final accepted artifacts are created.
- Stage 9 is collaborative: the user handles the physical device and the agent handles terminal checks.
- During physical work, give only one or two small actions at a time and compare before/after evidence.
- Do not claim a device is connected, accessible, or tested without terminal evidence or recorded user-visible evidence.
- Recheck external state such as USB visibility, addresses, and permissions when relevant; it may change between stages.

## Installation and permission rules

- Stage 7 assesses driver and library needs; it does not install them.
- Stage 8 follows the Stage 7 handoff narrowly. Do not restart discovery or add optional software.
- Prefer native/open support and the minimum required packages when supported by official evidence.
- Use an isolated Python environment by default when Python communication packages are required.
- Record environment paths, versions, sources, activation commands, and recreation steps.
- Never ask the user to reveal an administrator password.
- If tools cannot satisfy an interactive `sudo` prompt, give the user a narrow command to run and verify the result afterward.
- Do not create permission rules before observing the actual device identifiers and access problem.
- Do not install firmware, proprietary bundles, vendor utilities, or alternate drivers unless the upstream plan explicitly requires them.

## Safety and scope

- Start with read-only discovery and the safest documented interaction.
- Never claim a command, setting, acquisition, or performance result without actual evidence.
- Do not reset, autoset, calibrate, update firmware, enter service mode, bypass safety systems, or use undocumented commands unless explicitly required by a later approved stage.
- Preserve upstream defer/avoid lists.
- A successful identity query proves communication only, not acquisition correctness or scientific readiness.
- Keep temporary files under `/tmp`; keep persistent files in the appropriate stage folder.

## After Stage 9

Use `llm-device/Design/Workflow Sketch.xlsx` and the completed handoffs to identify the next roadmap task.

If that task has no dedicated instruction file:

1. Use the workflow sketch’s objective and inputs.
2. Treat the latest upstream plan as the scope boundary.
3. Create the next sequentially numbered folder using the workflow task name.
4. Implement the smallest safe proof first.
5. Produce appropriate code, human notes, machine-readable status, and real test evidence.
6. Do not jump ahead into later feature, facility, GUI, orchestration, or data-system work.

## Completion checklist

Before declaring a stage complete, confirm:

- The stage objective was achieved, or the result is accurately marked partial/blocked.
- Required user interaction or acceptance occurred.
- Required files exist in the correct numbered folder.
- Structured artifacts validate.
- Claims are supported by official sources or observed evidence.
- Remaining uncertainties are carried into the next handoff.
- The next action is clear.
- `llm-device/` remains untouched.
