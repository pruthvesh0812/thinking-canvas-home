---
name: canvas-element-check
description: Test in the real browser that a canvas element works after it is added or changed. Use whenever a new element is added to the canvas (node, frame, edge or similar) or an existing element's behaviour, size, layout or controls change. Checks create, drag, select, type, delete and reload.
---

# Canvas element check

Typecheck and unit tests do not catch a node that cannot be dragged or a
button nobody can click. Every time an element is added to the canvas, or an
existing one is altered (size, layout, handles, inputs, z-order), run this
check in the real app before saying it is done. Frontend repo only.

## Setup

1. The dev server usually already runs on `localhost:9000` (see
   `.claude/launch.json` in the frontend repo). Reuse it; it hot-reloads. If
   a change does not show, reload the page once before blaming the code.
2. Open it in the built-in browser pane. Use a throwaway canvas named like
   "UI test canvas" so real work is untouched. Creating one writes to the
   dev Supabase: tell the user, and name it so they can delete it.
3. Unit tests need Node 22: `PATH=~/.nvm/versions/node/v22.23.4/bin:$PATH npm test`
   (the default Node 18 cannot start vitest).

## The checklist

Run each step with real pointer actions (click, drag, type), not by calling
store functions. Take a screenshot after each. Do it for the new or changed
element AND for its neighbours (a new element can break the old ones).

1. **Created**: the button or gesture makes it appear, at a sensible place,
   with the right default size and look. No error in the dev log or console
   (`.next/dev/logs/next-development.log`, grep for `warn`/`error`).
2. **Layering**: it sits at the right depth (a frame stays behind nodes) and
   does not cover other controls.
3. **Dragged**: grab it where a user would (title bar, body, label) and
   drag. It must follow the pointer. A text input or button that fills the
   grab area blocks the drag: leave a free grip.
4. **Selected**: click it, then shift-click and marquee (Cmd/Ctrl-drag)
   with a neighbour. Selection highlight shows; the context bar reacts if
   it should.
5. **Typed on**: click into its text, type, press Enter or click away. The
   text stays, the element resizes as designed, and the keys do not trigger
   canvas shortcuts or a drag.
6. **Resized and controls**: resize handles, colour dots, add-context "+",
   and delete (x or Backspace) each respond. Delete asks for confirmation or
   offers undo where the app does that.
7. **Deleted**: it goes away, its neighbours stay, undo works if offered.
8. **Reload**: reload the page. Position, size, text and colour are still
   there (or the known stopgap, e.g. localStorage, is stated).
9. **Old behaviour**: the existing node still creates, drags, types and
   deletes. Cheapest way: one new node, drag it, type "A", delete it.

## Reporting

List each step as passed, failed or not tried. A failed step is fixed and
re-run, not described. Say what was never tested (for example, history view
or small screens). Finish with `npm run typecheck`, `npm run lint` and the
tests.
