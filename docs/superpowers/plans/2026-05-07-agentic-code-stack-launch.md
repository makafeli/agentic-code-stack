# Plan: Agentic Code Stack Public Launch

## Task 1: Git init + .gitignore
**Files:** `.gitignore` (new)
**Deps:** none
- Init git repo at project root
- Create `.gitignore` with `.superpowers/`, `.DS_Store` entries
**Verify:** `git status` shows clean working tree (only tracked files unstaged)

## Task 2: Rename stack-diagram.html → index.html
**Files:** `stack-diagram.html` → `index.html`
**Deps:** none
- Rename the file
- Update `<title>` from "Claude Code Stack" to "Agentic Code Stack"
- Update header `<h1><em>` text from "Claude Code Stack" to "Agentic Code Stack"
- Update `<div class="sub">` text if it references the old name
**Verify:** Open `index.html` in browser, confirm title and header show "Agentic Code Stack"

## Task 3: Create CLAUDE.md
**Files:** `CLAUDE.md` (new)
**Deps:** none
- Copy `AGENTS.md` content verbatim into `CLAUDE.md`
- No changes needed (content is agent-agnostic)
**Verify:** `diff AGENTS.md CLAUDE.md` shows no differences

## Task 4: Write PHILOSOPHY.md
**Files:** `PHILOSOPHY.md` (new)
**Deps:** none
- Write ~250-word philosophy doc per design spec
- Sections: The problem, The model, When to use full stack, When to go lightweight, How to customize, The meta point
**Verify:** Read the file, confirm all sections present, tone is reference-guide

## Task 5: Create community-recipes/README.md
**Files:** `community-recipes/README.md` (new)
**Deps:** none
- Create directory and README
- Explain the purpose: community-submitted stack variants and tool configs
- Define the submission format and bar (is it documented? does it work?)
- Reference the GitHub Discussions as companion
**Verify:** Read the file, confirm clear instructions for contributors

## Task 6: Polish README.md
**Files:** `README.md`
**Deps:** Task 4 (references philosophy)
- Update title and first paragraph: "Claude Code" → "Agentic Code Stack"
- Add philosophy hook at top referencing PHILOSOPHY.md
- Add links to GitHub Discussions, community-recipes/
- Update directory tree to reflect new structure (CLAUDE.md, PHILOSOPHY.md, community-recipes/)
- "Credits & sources" section stays, add note about community contributions
**Verify:** Read the file, confirm all old name references replaced

## Task 7: Update INSTALL.md naming
**Files:** `INSTALL.md`
**Deps:** none
- Replace "Claude Code Stack" references with "Agentic Code Stack" where referring to the project
- Keep "Claude Code" where referring to the CLI tool itself
**Verify:** `grep -n "Claude Code Stack" INSTALL.md` returns no results

## Task 8: Spot-check pipeline/ and rails/ docs
**Files:** `pipeline/*.md`, `rails/*.md`
**Deps:** none
- Grep all files for "Claude Code Stack" and replace with "Agentic Code Stack"
**Verify:** `grep -r "Claude Code Stack" pipeline/ rails/` returns no results

## Task 9: GitHub setup
**Deps:** Tasks 1-8 complete
- Create GitHub repo named "agentic-code-stack"
- Configure GitHub Pages: source = main branch, root directory
- Enable GitHub Discussions with 4 categories
- Push initial commit
**Verify:** Visit Pages URL, see diagram. Visit repo, see Discussions tab.

## Task 10: Visual companion cleanup
**Deps:** none (parallel)
- Push waiting.html to visual companion to clear stale screen
- Stop the visual companion server
**Verify:** Browser shows clean state
