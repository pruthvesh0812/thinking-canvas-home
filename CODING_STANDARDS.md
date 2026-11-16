---
last-verified: 2026-11-16
verified-against: thinking-canvas-be fix/recheck-focus-before-run-2026-11-08T0851 (2998d3c) and thinking-canvas-frontend feature/accept-reject-ghost-with-frames-change (d975c0f)
---

# Shared coding standards

Rules that hold in both canvas repos. Each repo's own `CODING_STANDARDS.md`
adds the rules for its stack:

- Backend: `../thinking-canvas-be/CODING_STANDARDS.md`
- Frontend: `../thinking-canvas-frontend/CODING_STANDARDS.md`

Read this file and the repo's file before writing or reviewing code.

---

## Four Working Rules

1. **Think First**: read the relevant context files before writing any code.
2. **Simplicity**: write the minimum code that satisfies the requirement.
3. **Surgical**: change only what the task requires.
4. **Goal-Driven**: every line must trace back to the task.

---

## Cross-repo Non-Negotiables

1. **No Supabase Realtime.** Neither repo calls `supabase.channel()`. The only
   server push is the backend's SSE ghost stream.
2. **`canvases.original_intent` is write-once.** It is inserted when the canvas
   is created and never updated: no backend UPDATE, no frontend edit control.
3. **Contract types have one source**: the backend's `types/index.ts`. The
   frontend mirrors them and never redefines them.
4. **Accepting a ghost is done by the backend.** `POST /api/ghost-status`
   calls `accept_ghost_node`, which creates the node, its edges and its frame
   in one transaction. The frontend writes nothing for an accept, and nothing
   is ever auto-accepted.
5. **Log through `logger` from `src/lib/logger.ts`**, never `console.*`
   directly (both repos keep the logger at that path).
6. **npm only.** No pnpm, yarn or bun.

---

## Git Branch Naming

```
<type>/<short-title>-<timestamp>
```

- **type**: one of `feature`, `fix`, `chore`, `refactor`, `docs`, `test`, matching the change's nature
- **short-title**: kebab-case, 2-5 words, describes the change itself (not a ticket number)
- **timestamp**: UTC, `YYYY-MM-DDTHHmm` (a colon isn't valid in git ref names, so the hour/minute pair has no separator)

```bash
# ✅
feature/rejection-insights-injection-2026-06-20T1430
fix/edge-selector-escape-2026-07-06T0915

# ❌ Never random/auto-generated names
claude/admiring-albattani-nfsixe
```

---

## Package Manager

```bash
npm install [package]
npm run dev / build / lint / test
```

---

## TypeScript

```typescript
// ❌ Never `any`
// ❌ Never a type assertion (`as X`) when a type guard can do it

// ✅ Narrow a discriminated union with a type guard instead of casting.
// When a new variant is added later, the compiler then forces every call
// site to be re-examined instead of silently reading a missing field.
function isGhostChunk(msg: RedisMessage): msg is Extract<RedisMessage, { type: 'ghost_chunk' }> {
  return msg.type === 'ghost_chunk'
}
```

---

## Prohibited Patterns

```typescript
// ❌ supabase.channel()        — Realtime is disabled product-wide
// ❌ original_intent update    — write-once by design
// ❌ contract types redefined  — backend types/index.ts is the source
// ❌ console.log               — use logger
// ❌ pnpm / yarn / bun         — npm only
```
