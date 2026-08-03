---
name: ralph-planner
description: "Scaffold autonomous AI-driven development using the Ralph Wiggum approach - a loop-based system where an AI agent picks tasks from a PRD, implements them, and tracks progress across context windows. Use when the user wants to plan a feature for autonomous implementation, create a PRD, set up Ralph scripts, or prepare a codebase for AFK coding. Triggers: ralph, ralph wiggum, ralph planner, plan feature, create prd, autonomous coding, afk coding, hitl script, ralph prompt, ralph scaffold."
---

## Overview

You are a senior engineer preparing a codebase for autonomous AI-driven development using the **Ralph Wiggum approach** -- a loop-based, autonomous coding system where an AI agent picks tasks from a PRD, implements them, and tracks progress across context windows.

Your job is to take the feature/task described by the user and produce all the scaffolding Ralph needs to run. Follow the **exact naming conventions, file structure, and script patterns** described below.

> **IMPORTANT:** This is a READ-ONLY planning template. Your job is to READ it and CREATE new files based on its instructions. Do not modify, edit, or delete this skill file.

## File Structure to Create

All planning files live under `.opencode/plans/` in the project root:

```
.opencode/plans/
  RALPH-PROMPT-<FEATURE>.md       # The per-iteration Ralph prompt
  prd-<feature>.json              # Structured PRD (living TODO list)
  plan-<feature>.md               # Human-readable implementation plan
progress-ralph-<feature>.txt            # Session progress tracker (project root)
hitl-ralph-<feature>.sh           # HITL single-iteration script (project root)
afk-ralph-<feature>.sh            # AFK loop script (project root)
ralph-<feature>-session.log       # Created at runtime by AFK script
```

**Derive `<feature>` from the task** as a short kebab-case name (e.g. `auth`, `slack-migration`, `payment-flow`). Use uppercase for `<FEATURE>` in the prompt filename only.

## Step 1: Create `.opencode/plans/plan-<feature>.md`

A human-readable implementation plan. Include:

- Overview of the feature and what "done" looks like
- Phases broken into logical chunks
- Architectural decisions and integration points (do first)
- Unknowns / spikes to investigate (do early)
- Standard implementation tasks
- Polish and cleanup (do last)
- Notes on which tasks are HITL vs. safe for AFK
- Running Ralph section (see Workflow Reminder at bottom of this file)

Keep it concise -- this is a reference, not a spec.

## Step 2: Create `.opencode/plans/prd-<feature>.json`

The PRD is both scope definition and progress tracker. Use this exact schema:

```json
{
  "prd": {
    "feature": "<feature name>",
    "description": "<brief description of the feature>",
    "created": "<ISO date>",
    "items": [
      {
        "id": "<feature>-001",
        "title": "<Short title>",
        "category": "architectural | functional | integration | polish",
        "priority": "1-critical | 2-high | 3-medium | 4-low",
        "description": "<Specific description of what needs to be done>",
        "steps_to_verify": [
          "Step 1 to verify this item is complete",
          "Step 2 to verify",
          "Edge case to check"
        ],
        "passes": false
      }
    ]
  }
}
```

Rules for PRD items:
- `id` format: `<feature>-001`, `<feature>-002`, etc.
- `priority` drives ordering: `1-critical` first, `4-low` last
- Items must be small -- one logical change each
- `steps_to_verify` must be explicit -- no vague acceptance criteria
- Always include edge cases as explicit steps (Ralph skips unstated ones)
- All items start with `"passes": false`
- Order: architectural -> integration -> unknowns -> features -> polish

Convert the user's feature requirements into structured PRD items.
Each item should have: category, description, steps to verify, and passes: false.
Be specific about acceptance criteria.

## Step 3: Create `.opencode/plans/RALPH-PROMPT-<FEATURE>.md`

This is the prompt passed to Ralph every iteration. Use this content verbatim, substituting `<feature>` and `<Feature Name>` placeholders:

````markdown
# Ralph Wiggum - <Feature Name>

@.opencode/plans/prd-<feature>.json @progress-ralph-<feature>.txt

You are working autonomously on the <Feature Name> implementation using the Ralph Wiggum approach.

## Your Task This Iteration

1. Read `prd-<feature>.json` -- find all items where `passes: false`
2. Read `progress-ralph-<feature>.txt` -- understand what has already been done
3. Choose the single highest-priority incomplete task (lowest priority number wins)
4. Implement it fully, following all steps_to_verify
5. Run ALL feedback loops -- fix any failures before committing
6. Update `prd-<feature>.json`: set `passes: true` for the completed item
7. Append your progress to `progress-ralph-<feature>.txt`
8. Make a single focused git commit
9. If ALL items in `prd-<feature>.json` have `passes: true`, emit: <promise>COMPLETE</promise>

ONLY WORK ON A SINGLE PRD ITEM PER ITERATION.

---

## PRD Item Schema

Each item in prd-<feature>.json:

{
  "id": "<feature>-001",
  "title": "Short title",
  "category": "architectural | functional | integration | polish",
  "priority": "1-critical | 2-high | 3-medium | 4-low",
  "description": "What needs to be done",
  "steps_to_verify": ["verification step 1", "verification step 2"],
  "passes": false
}

---

## Progress Tracking

After completing each task, append to progress-ralph-<feature>.txt:
- Task completed and PRD item reference
- Key decisions made and reasoning
- Files changed
- Any blockers or notes for next iteration

Keep entries concise. Sacrifice grammar for the sake of concision. This file helps future iterations skip exploration.

---

## Feedback Loops

Before committing, run ALL feedback loops:
1. TypeScript: npm run typecheck (must pass with no errors)
2. Tests: npm run test (must pass)
3. Lint: npm run lint (must pass)

Do NOT commit if any feedback loop fails. Fix issues first.

---

## Step Size

Keep changes small and focused:
- One logical change per commit
- If a task feels too large, break it into subtasks
- Prefer multiple small commits over one large commit
- Run feedback loops after each change, not at the end

Quality over speed. Small steps compound into big progress.

---

## Task Prioritization

When choosing the next task, prioritize in this order:
1. Architectural decisions and core abstractions
2. Integration points between modules
3. Unknown unknowns and spike work
4. Standard features and implementation
5. Polish, cleanup, and quick wins

Fail fast on risky work. Save easy wins for later.

---

## Code Quality

This codebase will outlive you. Every shortcut you take becomes
someone else's burden. Every hack compounds into technical debt
that slows the whole team down.

You are not just writing code. You are shaping the future of this
project. The patterns you establish will be copied. The corners
you cut will be cut again.

Fight entropy. Leave the codebase better than you found it.
````

## Step 4: Create `hitl-ralph-<feature>.sh`

HITL script for supervised single-iteration runs. Use this exact pattern:

```bash
#!/bin/bash
# hitl-ralph-<feature>.sh - HITL (Human-In-The-Loop) single iteration for <Feature Name>
# Usage: ./hitl-ralph-<feature>.sh
#
# Run this to execute ONE Ralph iteration while watching.
# Use this to refine your prompt before going AFK.
#
# Based on the Ralph Wiggum approach by Matt Pocock
# https://www.totaltypescript.com/ralph-wiggum

set -e

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_ROOT="$SCRIPT_DIR"
PRD_FILE="$PROJECT_ROOT/.opencode/plans/prd-<feature>.json"
PROGRESS_FILE="$PROJECT_ROOT/progress-ralph-<feature>.txt"
PROMPT_FILE="$PROJECT_ROOT/.opencode/plans/RALPH-PROMPT-<FEATURE>.md"

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
CYAN='\033[0;36m'
NC='\033[0m' # No Color

# JSON parsing helper using Node.js (more portable than jq)
get_total_items() {
    node -e "const data = require('$PRD_FILE'); console.log(data.prd.items.length);"
}

get_completed_items() {
    node -e "const data = require('$PRD_FILE'); console.log(data.prd.items.filter(i => i.passes === true).length);"
}

get_next_items() {
    node -e "
const data = require('$PRD_FILE');
const pending = data.prd.items
    .filter(i => i.passes === false)
    .sort((a, b) => a.priority.localeCompare(b.priority))
    .slice(0, 3);
pending.forEach(i => console.log('  [' + i.priority + '] ' + i.id + ': ' + i.title));
"
}

echo -e "${CYAN}========================================${NC}"
echo -e "${CYAN}  RALPH WIGGUM - HITL Mode              ${NC}"
echo -e "${CYAN}  <Feature Name>                         ${NC}"
echo -e "${CYAN}========================================${NC}"
echo ""

# Check required files exist
if [ ! -f "$PRD_FILE" ]; then
    echo -e "${RED}ERROR: PRD file not found: $PRD_FILE${NC}"
    exit 1
fi

if [ ! -f "$PROMPT_FILE" ]; then
    echo -e "${RED}ERROR: Prompt file not found: $PROMPT_FILE${NC}"
    exit 1
fi

# Show current progress
echo -e "${BLUE}Current Progress:${NC}"
echo "----------------------------------------"
TOTAL_ITEMS=$(get_total_items)
COMPLETED=$(get_completed_items)
REMAINING=$((TOTAL_ITEMS - COMPLETED))

echo -e "  Total PRD items:     ${TOTAL_ITEMS}"
echo -e "  Completed:           ${GREEN}${COMPLETED}${NC}"
echo -e "  Remaining:           ${YELLOW}${REMAINING}${NC}"
echo ""

# Show next items to work on
echo -e "${BLUE}Next items in queue (by priority):${NC}"
get_next_items
echo ""

# Show recent progress entries
echo -e "${BLUE}Recent progress entries:${NC}"
echo "----------------------------------------"
tail -15 "$PROGRESS_FILE" 2>/dev/null | grep -v "^#" | grep -v "^$" | tail -10 || echo "  (no entries yet)"
echo ""

# Run feedback loops to show current state
echo -e "${YELLOW}Running feedback loops to check current state...${NC}"
echo ""

echo -e "${BLUE}[1/3] TypeScript Check:${NC}"
if npm run typecheck 2>&1; then
    echo -e "${GREEN}  ✓ TypeScript OK${NC}"
else
    echo -e "${RED}  ✗ TypeScript errors found${NC}"
fi
echo ""

echo -e "${BLUE}[2/3] Lint Check:${NC}"
if npm run lint --silent 2>&1 | tail -5; then
    echo -e "${GREEN}  ✓ Lint OK${NC}"
else
    echo -e "${RED}  ✗ Lint errors found${NC}"
fi
echo ""

echo -e "${BLUE}[3/3] Test Check:${NC}"
if npm run test --silent 2>&1 | tail -10; then
    echo -e "${GREEN}  ✓ Tests OK${NC}"
else
    echo -e "${RED}  ✗ Test failures found${NC}"
fi
echo ""

echo -e "${CYAN}========================================${NC}"
echo -e "${CYAN}  Starting Ralph iteration...          ${NC}"
echo -e "${CYAN}  Press Ctrl+C to abort                ${NC}"
echo -e "${CYAN}========================================${NC}"
echo ""

# Build the prompt with file references
RALPH_PROMPT=$(cat "$PROMPT_FILE")

# Build the full message for OpenCode
MESSAGE="$RALPH_PROMPT

## Context Files

The following files are attached for context:
- prd-<feature>.json (PRD with all items and their status)
- progress-ralph-<feature>.txt (session progress tracker)

## Current State

- Project root: $PROJECT_ROOT
- Completed items: $COMPLETED / $TOTAL_ITEMS
- Mode: HITL (Human-In-The-Loop)

## Instructions

Work on ONE task from the PRD. Follow the steps_to_verify for that item.
Run all feedback loops before committing.
Update progress-ralph-<feature>.txt when done.
"

# Run OpenCode with the Ralph prompt
cd "$PROJECT_ROOT"
opencode run "$MESSAGE" \
    --file "$PRD_FILE" \
    --file "$PROGRESS_FILE" \
    --title "Ralph HITL: <Feature Name>"

echo ""
echo -e "${CYAN}========================================${NC}"
echo -e "${CYAN}  Ralph iteration complete!            ${NC}"
echo -e "${CYAN}========================================${NC}"
echo ""

# Show updated progress
COMPLETED_AFTER=$(get_completed_items)
if [ "$COMPLETED_AFTER" -gt "$COMPLETED" ]; then
    echo -e "${GREEN}Progress: ${COMPLETED} → ${COMPLETED_AFTER} / ${TOTAL_ITEMS} items complete${NC}"
    echo -e "${GREEN}+$((COMPLETED_AFTER - COMPLETED)) item(s) completed this iteration!${NC}"
else
    echo -e "${YELLOW}Progress: ${COMPLETED_AFTER} / ${TOTAL_ITEMS} items complete (no change)${NC}"
fi
echo ""

# Show git status
echo -e "${BLUE}Git status:${NC}"
cd "$PROJECT_ROOT"
git status --short 2>/dev/null | head -10 || true
```

## Step 5: Create `afk-ralph-<feature>.sh`

AFK loop script for unsupervised runs. Use this exact pattern:

```bash
#!/bin/bash
# afk-ralph-<feature>.sh - AFK (Away From Keyboard) Ralph loop for <Feature Name>
# Usage: ./afk-ralph-<feature>.sh <max_iterations>
#
# Runs Ralph autonomously up to <max_iterations> times.
# Will exit early if all PRD items are complete.
#
# SAFETY: Always cap iterations. Recommended: 5-10 for small tasks, 25 for larger ones.
#
# Based on the Ralph Wiggum approach by Matt Pocock
# https://www.totaltypescript.com/ralph-wiggum

set -e

if [ -z "$1" ]; then
    echo "Usage: $0 <max_iterations>"
    echo ""
    echo "Examples:"
    echo "  $0 5    # Quick run - 5 iterations"
    echo "  $0 10   # Medium run - 10 iterations"
    echo "  $0 25   # Full run - one per PRD item"
    echo ""
    echo "Tip: Start with ./hitl-ralph-<feature>.sh to validate your prompt first!"
    exit 1
fi

MAX_ITERATIONS=$1
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_ROOT="$SCRIPT_DIR"
PRD_FILE="$PROJECT_ROOT/.opencode/plans/prd-<feature>.json"
PROGRESS_FILE="$PROJECT_ROOT/progress-ralph-<feature>.txt"
PROMPT_FILE="$PROJECT_ROOT/.opencode/plans/RALPH-PROMPT-<FEATURE>.md"
LOG_FILE="$PROJECT_ROOT/ralph-<feature>-session.log"

# Colors
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
CYAN='\033[0;36m'
NC='\033[0m'

# JSON parsing helpers using Node.js (more portable than jq)
get_total_items() {
    node -e "const data = require('$PRD_FILE'); console.log(data.prd.items.length);"
}

get_completed_items() {
    node -e "const data = require('$PRD_FILE'); console.log(data.prd.items.filter(i => i.passes === true).length);"
}

get_next_item() {
    node -e "
const data = require('$PRD_FILE');
const pending = data.prd.items
    .filter(i => i.passes === false)
    .sort((a, b) => a.priority.localeCompare(b.priority));
if (pending.length > 0) {
    console.log(pending[0].id + ': ' + pending[0].title);
} else {
    console.log('ALL COMPLETE');
}
"
}

# Log function
log() {
    local msg="[$(date '+%Y-%m-%d %H:%M:%S')] $1"
    echo -e "$msg"
    echo "$msg" >> "$LOG_FILE"
}

echo -e "${CYAN}========================================${NC}"
echo -e "${CYAN}  RALPH WIGGUM - AFK Mode               ${NC}"
echo -e "${CYAN}  <Feature Name>                         ${NC}"
echo -e "${CYAN}========================================${NC}"
echo -e "  Max iterations: ${YELLOW}$MAX_ITERATIONS${NC}"
echo -e "  Log file: $LOG_FILE"
echo ""

# Check required files exist
if [ ! -f "$PRD_FILE" ]; then
    echo -e "${RED}ERROR: PRD file not found: $PRD_FILE${NC}"
    exit 1
fi

if [ ! -f "$PROMPT_FILE" ]; then
    echo -e "${RED}ERROR: Prompt file not found: $PROMPT_FILE${NC}"
    exit 1
fi

# Initialize log file
echo "========================================" >> "$LOG_FILE"
echo "AFK Ralph <Feature Name> Session Started: $(date)" >> "$LOG_FILE"
echo "Max iterations: $MAX_ITERATIONS" >> "$LOG_FILE"
echo "========================================" >> "$LOG_FILE"

START_TIME=$(date +%s)
TOTAL_ITEMS=$(get_total_items)

# Pre-flight check
log "${YELLOW}Running pre-flight checks...${NC}"

echo -e "${BLUE}TypeScript:${NC}"
if npm run typecheck 2>&1 | tail -3; then
    echo -e "${GREEN}  ✓ OK${NC}"
else
    echo -e "${YELLOW}  ⚠ Errors exist (will be fixed during run)${NC}"
fi

echo -e "${BLUE}Lint:${NC}"
if npm run lint --silent 2>&1 | tail -3; then
    echo -e "${GREEN}  ✓ OK${NC}"
else
    echo -e "${YELLOW}  ⚠ Errors exist (will be fixed during run)${NC}"
fi

echo ""
log "${CYAN}Starting AFK loop...${NC}"
echo ""

# Read the prompt template once
RALPH_PROMPT=$(cat "$PROMPT_FILE")

# Main loop
ITERATIONS_RUN=0
for ((i=1; i<=$MAX_ITERATIONS; i++)); do
    ITERATIONS_RUN=$i

    echo ""
    echo -e "${CYAN}========================================${NC}"
    echo -e "${CYAN}  Iteration $i of $MAX_ITERATIONS${NC}"
    echo -e "${CYAN}  $(date '+%Y-%m-%d %H:%M:%S')${NC}"
    echo -e "${CYAN}========================================${NC}"

    log "Starting iteration $i"

    # Check if all items are complete
    COMPLETED=$(get_completed_items)
    REMAINING=$((TOTAL_ITEMS - COMPLETED))

    echo -e "${BLUE}Progress: ${GREEN}${COMPLETED}${NC}/${TOTAL_ITEMS} complete, ${YELLOW}${REMAINING}${NC} remaining${NC}"
    log "Progress: $COMPLETED/$TOTAL_ITEMS complete"

    if [ "$COMPLETED" -eq "$TOTAL_ITEMS" ]; then
        log "${GREEN}All PRD items complete! Exiting loop.${NC}"
        break
    fi

    # Show which item is likely next
    NEXT_ITEM=$(get_next_item)
    echo -e "  Next likely item: ${YELLOW}$NEXT_ITEM${NC}"

    # Build the full message for OpenCode
    MESSAGE="$RALPH_PROMPT

## Context Files

The following files are attached for context:
- prd-<feature>.json (PRD with all items and their status)
- progress-ralph-<feature>.txt (session progress tracker)

## Current State

- Project root: $PROJECT_ROOT
- Completed items: $COMPLETED / $TOTAL_ITEMS
- Iteration: $i of $MAX_ITERATIONS
- Mode: AFK (Autonomous)

## Instructions

Work on ONE task from the PRD. Follow the steps_to_verify for that item.
Run all feedback loops before committing.
Update progress-ralph-<feature>.txt when done.

If ALL items in prd-<feature>.json have \"passes\": true, output exactly:
<promise>COMPLETE</promise>
"

    log "Invoking OpenCode..."

    # Run OpenCode with the Ralph prompt
    cd "$PROJECT_ROOT"
    RESULT=$(opencode run "$MESSAGE" \
        --file "$PRD_FILE" \
        --file "$PROGRESS_FILE" \
        --title "Ralph AFK $i/$MAX_ITERATIONS: <Feature Name>" \
        2>&1) || true

    # Log output (truncated)
    echo "$RESULT" | tail -50
    echo "$RESULT" | tail -20 >> "$LOG_FILE"

    # Check for completion signal
    if [[ "$RESULT" == *"<promise>COMPLETE</promise>"* ]]; then
        log "${GREEN}Received COMPLETE signal. All PRD items done!${NC}"
        break
    fi

    # Check for errors that should stop the loop
    if [[ "$RESULT" == *"ERROR"* ]] || [[ "$RESULT" == *"FATAL"* ]]; then
        log "${RED}Error detected in output. Consider reviewing before continuing.${NC}"
        # Don't exit - let Ralph try to fix it next iteration
    fi

    # Brief pause between iterations to avoid rate limits
    log "Iteration $i complete. Pausing 3s before next..."
    sleep 3
done

END_TIME=$(date +%s)
DURATION=$((END_TIME - START_TIME))
DURATION_MIN=$((DURATION / 60))
DURATION_SEC=$((DURATION % 60))

echo ""
echo -e "${CYAN}========================================${NC}"
echo -e "${CYAN}  AFK Ralph Session Complete            ${NC}"
echo -e "${CYAN}========================================${NC}"
echo ""

# Final status
FINAL_COMPLETED=$(get_completed_items)

echo -e "  Duration:        ${DURATION_MIN}m ${DURATION_SEC}s"
echo -e "  Iterations run:  $ITERATIONS_RUN"
echo -e "  Items completed: ${GREEN}${FINAL_COMPLETED}${NC} / ${TOTAL_ITEMS}"
echo ""

log "Session complete. Duration: ${DURATION_MIN}m ${DURATION_SEC}s, Iterations: $ITERATIONS_RUN"

# Run final feedback loops
echo -e "${YELLOW}Running final feedback loops...${NC}"
echo ""

echo -e "${BLUE}[1/3] TypeScript Check:${NC}"
if npm run typecheck 2>&1; then
    echo -e "${GREEN}  ✓ TypeScript OK${NC}"
else
    echo -e "${RED}  ✗ TypeScript errors - manual review needed${NC}"
fi

echo -e "${BLUE}[2/3] Lint Check:${NC}"
if npm run lint --silent 2>&1 | tail -5; then
    echo -e "${GREEN}  ✓ Lint OK${NC}"
else
    echo -e "${RED}  ✗ Lint errors - manual review needed${NC}"
fi

echo -e "${BLUE}[3/3] Test Check:${NC}"
if npm run test --silent 2>&1 | tail -10; then
    echo -e "${GREEN}  ✓ Tests OK${NC}"
else
    echo -e "${RED}  ✗ Test failures - manual review needed${NC}"
fi

echo ""
echo -e "${BLUE}Git log (recent commits):${NC}"
cd "$PROJECT_ROOT"
git log --oneline -10 2>/dev/null || true

echo ""
echo -e "${GREEN}Review the changes:${NC}"
echo "  git log --oneline -20"
echo "  git diff HEAD~$ITERATIONS_RUN"
echo ""
echo -e "${CYAN}Session log saved to: $LOG_FILE${NC}"
```

## Workflow Reminder

Include this section in `plan-<feature>.md`:

```markdown
## Running Ralph

### Start here: HITL (watch one iteration, refine the prompt)
./hitl-ralph-<feature>.sh

### Once prompt is validated: AFK loop
./afk-ralph-<feature>.sh 5    # Quick run
./afk-ralph-<feature>.sh 10   # Medium run
./afk-ralph-<feature>.sh 25   # Full run - one per PRD item

### Monitor progress
tail -f progress-ralph-<feature>.txt
tail -f ralph-<feature>-session.log

### Review when done
git log --oneline -20
git diff HEAD~<N>
```

## Output Checklist

Before finishing, confirm you have created all files. Replace every `<feature>`, `<FEATURE>`, and `<Feature Name>` placeholder with the actual values derived from the task:

- [ ] `.opencode/plans/plan-<feature>.md` -- human-readable implementation plan (includes Running Ralph section)
- [ ] `.opencode/plans/prd-<feature>.json` -- structured PRD, all items `passes: false`
- [ ] `.opencode/plans/RALPH-PROMPT-<FEATURE>.md` -- Ralph iteration prompt (use template verbatim)
- [ ] `hitl-ralph-<feature>.sh` -- HITL single-iteration script
- [ ] `afk-ralph-<feature>.sh` -- AFK loop script
- [ ] `progress-ralph-<feature>.txt` -- create as empty file

Also ensure:
- `.opencode/plans/` directory exists
- Shell scripts are executable: `chmod +x hitl-ralph-<feature>.sh afk-ralph-<feature>.sh`
- Node.js is used for JSON parsing (not jq)

## Now: Read the task below and produce all five files.

> **TASK:**
> *(The user's feature/task description will follow when this skill is invoked)*
