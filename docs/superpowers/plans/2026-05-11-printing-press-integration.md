# Plan: Printing Press Integration

## Task 1: Create rails/05-agent-interfaces.md
**Files:** `rails/05-agent-interfaces.md` (new)
**Deps:** none
- Write the full rail doc per design spec (~80 lines)
- Follow the structure of existing rails (tool table, setup, when-to-engage, cross-reference)
**Verify:** `cat rails/05-agent-interfaces.md` shows all 7 sections present

## Task 2: Update AGENTS.md + CLAUDE.md
**Files:** `AGENTS.md`, `CLAUDE.md`
**Deps:** none (can run parallel with Task 1)
- Add "Rail 05 — Agent Interfaces: Printing Press" line to the Rails section in both files
- Add `rails/05-agent-interfaces.md` to the detail files section in both files
**Verify:** `grep "Rail 05" AGENTS.md` and `CLAUDE.md` return one match each

## Task 3: Update index.html (new rail card)
**Files:** `index.html`
**Deps:** none (can run parallel with 1-2)
- Add the 5th rail `<div class="rail">` block for Agent Interfaces before the closing `</div>` of the rails grid
- Follow the existing rail card structure: rail-head (tag, h4, desc) + rail-body (tool entries)
- Tool entries: Printing Press (binary + library), with setup commands in .cmds divs
**Verify:** Open `index.html` in browser, confirm 5 rails visible in 3+2 wrapping layout

## Task 4: Update README.md
**Files:** `README.md`
**Deps:** none
- Add `rails/05-agent-interfaces.md` to the directory tree
**Verify:** `grep "05-agent-interfaces" README.md` returns a match

## Task 5: Cross-reference in rails/04-external-data.md
**Files:** `rails/04-external-data.md`
**Deps:** none
- Add Printing Press to the cross-reference section for structured service interaction vs read-only scraping
**Verify:** `grep "Printing Press" rails/04-external-data.md` returns a match

## Task 6: Commit all changes
**Deps:** Tasks 1-5
- Stage all changed files and commit with a descriptive message
**Verify:** `git log -1` shows the commit, `git status` is clean
