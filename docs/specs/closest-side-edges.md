# Spec: edges attach to the closest side of each node

Repo that owns the code: `thinking-canvas-frontend`. No backend change.

## Problem

Edges created by accepting an AI ghost have `from_handle` / `to_handle` = null, so React Flow falls back to the top handle. They run top-to-top regardless of where the nodes sit.

## Behaviour

1. **Closest side.** For an edge between nodes A and B, take the direction between the node centers. The dominant axis decides:
   - |dx| >= |dy|: horizontal. If B is right of A, A uses RIGHT and B uses LEFT; mirrored if B is left of A.
   - otherwise vertical. If B is below A, A uses BOTTOM and B uses TOP; mirrored if above.
   Center is computed from node position plus measured width/height.
2. **At accept time.** When an accepted ghost's edges land on the canvas, compute and set their sides immediately, before any drag.
3. **On drag stop only.** Use React Flow's `onNodeDragStop`. Never recalculate during the drag.
4. **Scope.** Every edge touching a dragged node whose two ends are both human nodes: accepted-AI edges and hand-drawn edges alike. Hand-drawn edges lose the side the user picked. Frame nodes, ghost nodes and their edges are left alone.
5. **Multi-select drag.** Collect the edges touching any dragged node, re-route each once, in a single batch.
6. **Bend points.** If an edge's sides change, reset its bend to null. If the sides are unchanged, leave the edge and its bend untouched.
7. **Persistence.** Write the new `from_handle` / `to_handle` (uppercase TOP/RIGHT/BOTTOM/LEFT, per the existing `handleSide` convention) to the Supabase `edges` rows through the existing persistence hook. A failed write is logged as a warning and the in-memory sides are kept, with no rollback.
8. **Read-only mode.** No re-routing and no writes.
9. **Undo.** A re-route folds into the node-move history step so one undo reverts both. If history does not track edge handles today, undo is out of scope; note that in the PR.

## Where it lands (to verify while implementing)

- `src/components/canvas/Canvas.tsx`: add `onNodeDragStop`.
- `src/stores/canvas-store.ts`: an action that updates an edge's `sourceHandle` / `targetHandle` (and clears bend).
- `src/hooks/use-canvas-persistence.ts`: update `from_handle` / `to_handle` on existing edge rows.
- Pure helper for the closest-side calculation, unit-tested with Vitest.
- Accept path: confirm the backend accept response does not overwrite the sides (null from the backend, so the frontend sets them after hydration or on arrival).

## Tests

- Closest-side helper: right, left, above, below, diagonal tie (|dx| == |dy|), overlapping nodes.
- Drag stop re-routes touching edges only; ghost and frame edges untouched.
- No change in sides leaves the bend intact; a change clears it.
- Multi-select: one batch.
- Read-only: no store change, no write.

## Out of scope

Continuous re-routing during drag, handle choice for ghost or frame edges, backend or contract changes.
