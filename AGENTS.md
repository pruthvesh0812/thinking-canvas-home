# thinking-canvas-home

Home Repo for ThinkingCanvas. Start agent sessions here. It holds the skills
and standards shared across the repos below, and points at each one. Code
stays in its own repo.

## Working across repos

- Before touching a repo, read its `CLAUDE.md`. It does not load automatically
  from here.
- To get a repo's own skills (for example the backend's `update-ai-context`),
  add it to the session with `/add-dir <path>`, or start with
  `claude --add-dir ../thinking-canvas-be --add-dir ../thinking-canvas-frontend`.
  The file access granted in `.claude/settings.json` does not load skills.
- Coding standards: `CODING_STANDARDS.md` here holds the rules both canvas
  repos share. Each repo's own `CODING_STANDARDS.md` holds its stack-specific
  rules. Read both before writing or reviewing code in a repo.
- The API contract spans both canvas repos. The backend's
  `.ai/context/FRONTEND-CONTRACT.md` and `types/index.ts` are the authority.
  The frontend's `.ai/context/API-CONTRACT.md` and `types/index.ts` mirror
  them. Change the backend first, then sync the frontend with its
  `.ai/skills/sync-contract-types.md`.
- Plan features that span repos here. Branch, commit and open pull requests in
  the repo that owns the code.
- For parallel work, create one git worktree per feature inside that repo.
- File each issue in the tracker of the repo it belongs to.

## Shared skills

- `code-alignment`: after a commit that changes logic, update the feature's
  alignment doc and run a short comprehension check.
- `stepping-stones`: walk a flow's real code one function at a time.

## Other Repos

- thinking-canvas-be (ThinkingCanvas API: Hono backend that runs the AI agent
  pipeline and streams ghost structures): ~/Desktop/thinking-canvas-be. Clone
  from https://github.com/pruthvesh0812/thinking-canvas-be.git if missing.
  Read its CLAUDE.md first.
- thinking-canvas-frontend (ThinkingCanvas Web: Next.js canvas UI on React Flow
  and Zustand): ~/Desktop/thinking-canvas-frontend. Clone from
  https://github.com/pruthvesh0812/thinking-canvas-frontend.git if missing.
  Read its CLAUDE.md first.
- thinking-loop (personal build, think, learn notes log; not a codebase):
  ~/Desktop/thinking-loop. Clone from
  https://github.com/pruthvesh0812/thinking-loop.git if missing. Read its
  CLAUDE.md first: commit straight to main, never branch.
