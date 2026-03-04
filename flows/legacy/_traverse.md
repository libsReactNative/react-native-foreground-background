# Traversal State

> Persistent recursion stack for tree traversal. AI reads this to know where it is and what to do next.

## Mode

- **BFS** (no comment): Breadth-first, analyze all domains systematically
- **DFS** (with comment): Depth-first, focus deeply on specific topic

## Source Path

[project root]

## Focus (DFS only)

[none]

## Algorithm

```
RECURSIVE-UNDERSTAND(node):
    1. ENTER: Push node to stack, set phase = ENTERING
    2. EXPLORE: Read code, form understanding, set phase = EXPLORING
    3. SPAWN: Identify children (deeper concepts), set phase = SPAWNING
    4. RECURSE: For each child -> RECURSIVE-UNDERSTAND(child)
    5. SYNTHESIZE: Combine children insights, set phase = SYNTHESIZING
    6. EXIT: Pop from stack, bubble up summary, set phase = EXITING
```

## Current Stack

> Read top-to-bottom = root-to-current. Last item = where AI is now.

```
/ (root)                           EXITING
```

Example when running:
```
/ (root)                           DONE
└── core-domain                    DONE
    └── authentication             EXPLORING <- current
        └── token-management       PENDING
```

## Stack Operations Log

| # | Operation | Node | Phase | Result |
|---|-----------|------|-------|--------|
| 1 | SPAWN | / (root) | SPAWNING | Identified 3 children: native-bridge, foreground-management, package-structure |

## Current Position

- **Node**: / (root)
- **Phase**: EXITING (analysis complete)
- **Depth**: 0
- **Path**: /

## Stack Operations Log

| # | Operation | Node | Phase | Result |
|---|-----------|------|-------|--------|
| 1 | SPAWN | / (root) | SPAWNING | Identified 3 children: native-bridge, foreground-management, package-structure |
| 2 | RECURSE | native-bridge | ENTERING | Explored method-binding, found critical bugs |
| 3 | EXIT | native-bridge | EXITING | Bubble up: module name mismatch, empty toBackground() |
| 4 | RECURSE | foreground-management | ENTERING | Explored power-management, activity-launching |
| 5 | EXIT | foreground-management | EXITING | Bubble up: deprecated API, CAR mode flag |
| 6 | RECURSE | package-structure | ENTERING | Leaf node analysis |
| 7 | EXIT | package-structure | EXITING | Bubble up: standard RN package, v0.0.2 |
| 8 | SYNTH | / (root) | SYNTHESIZING | Combined all children insights |
| 9 | COMPLETE | / (root) | EXITING | Analysis complete, ready to generate flows |

## Pending Children

> Children identified but not yet explored (LIFO - last added explored first)

```
[none - all children completed]
```

## Visited Nodes

> Completed nodes with their summaries

| Node Path | Summary | Flow Created |
|-----------|---------|--------------|
| package-structure | Standard React Native package, Android-only, v0.0.2 | SDD recommended |
| foreground-management | Screen wake + activity launch for incoming calls, deprecated API | SDD recommended |
| power-management | Deprecated FULL_WAKE_LOCK, 10s timeout | None |
| activity-launching | Dynamic class resolution, CAR mode flag | None |
| native-bridge | React Native bridge with bugs: module name mismatch, empty toBackground() | TDD recommended |
| method-binding | @ReactMethod binding, module name mismatch bug, toBackground() empty | None - bugs found |
| package-structure | Standard React Native package, Android-only, v0.0.2 | SDD recommended |

## Next Action

```
ANALYSIS COMPLETE - Flows Generated:

Created:
1. flows/tdd-native-bridge/ (CRITICAL - bugs found)
   - 01-requirements.md
   - 02-specifications.md
   - 03-tests.md

2. flows/sdd-foreground-management/ (HIGH - deprecated API)
   - 01-requirements.md
   - 02-specifications.md

Next Steps:
1. Review TDD flow and implement fixes
2. Fix module name mismatch in src/ForegroundBackground.js
3. Implement toBackground() method
4. Modernize WakeLock usage per SDD

State: READY_FOR_IMPLEMENTATION
```

---

## Phase Definitions

### ENTERING
- Just arrived at this node
- Create _node.md file
- Read relevant source files
- Form initial hypothesis

### EXPLORING
- Deep analysis of this node's scope
- Validate/refine hypothesis
- Identify what belongs here vs. children

### SPAWNING
- Identify child concepts that need deeper exploration
- Add children to Pending stack
- Children are LOGICAL concepts, not filesystem paths

### SYNTHESIZING
- All children completed (or no children)
- Combine insights from children
- Update this node's _node.md with full understanding

### EXITING
- Pop from stack
- Bubble up summary to parent
- Mark as visited

---

## Resume Protocol

When `/legacy` starts:
1. Read _traverse.md
2. Find current position (top of stack)
3. Check phase
4. Continue from that phase

If interrupted mid-phase:
- Re-enter same phase (idempotent operations)

---

*Updated by /legacy recursive traversal*
