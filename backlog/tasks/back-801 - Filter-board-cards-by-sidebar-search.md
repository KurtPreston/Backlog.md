---
id: BACK-801
title: Filter board cards by sidebar search
status: Done
assignee:
  - '@KurtPreston'
created_date: '2026-11-11T22:55:00Z'
updated_date: '2026-11-11T23:52:00Z'
labels:
  - web
  - enhancement
dependencies: []
references:
  - https://github.com/MrLesk/Backlog.md/issues/1021
ordinal: 407000
---

## Description

<!-- SECTION:DESCRIPTION:BEGIN -->
Typing in the sidebar search box lists a few matching tasks, but the Kanban board keeps showing every card, so there is no way to narrow the board to tasks matching some text. The board's dropdown filters (assignee, labels, type, project, priority) cover fields but not free text. Related request: https://github.com/MrLesk/Backlog.md/issues/1021
<!-- SECTION:DESCRIPTION:END -->

## Acceptance Criteria
<!-- AC:BEGIN -->
- [x] #1 While the board is open, a non-empty sidebar search query hides board cards that do not match it
- [x] #2 Board matching uses the same query syntax and task matching as the sidebar search results, including field filters such as status: and priority:
- [x] #3 The search filter combines with the board's dropdown filters, so a card shows only when it matches all of them
- [x] #4 Clearing the sidebar search, or using the board's Clear filters button, shows the unfiltered board again
<!-- AC:END -->

## Definition of Done
<!-- DOD:BEGIN -->
- [x] #1 bun run check:types passes when TypeScript touched
- [x] #2 bun run check . passes when formatting/linting touched
- [x] #3 bun test (or scoped test) passes
<!-- DOD:END -->

## Implementation Plan

<!-- SECTION:PLAN:BEGIN -->
1. Lift the sidebar search query from SideNavigation into App and pass it through Layout, so the sidebar and board share one value.
2. In Board, parse the query with parseSearchCommandQuery and match tasks with the shared createTaskSearchIndex (the same matcher behind the search API), after the existing dropdown filters.
3. Count the search as an active board filter so Clear filters also clears it.
4. Extend web-board-filters tests: search hides non-matching cards, combines with a dropdown filter, and Clear filters resets it.
<!-- SECTION:PLAN:END -->

## Implementation Notes

<!-- SECTION:NOTES:BEGIN -->
Validation: bun run check:types and npx biome check . pass. Focused web-board-filters, web-side-navigation-loading and web-task-project tests pass; the two new board tests fail without the Board change. Full bun run test ran on a heavily loaded machine; every failing file passed when rerun alone except scripts/update-nix.test.cjs, which fails identically on main (needs the patched Node toolchain). Built dist/backlog and checked in a browser against a real board: typing in the sidebar search narrowed 82 cards to 26, adding a priority dropdown narrowed to 7, and Clear filters restored all 82 and emptied the search box.
<!-- SECTION:NOTES:END -->

## Final Summary

<!-- SECTION:FINAL_SUMMARY:BEGIN -->
The sidebar search query now lives in App and is shared with the board. Board parses it with parseSearchCommandQuery and matches with the shared createTaskSearchIndex, the same matcher behind /api/search, after its dropdown filters, so cards must match both. Clear filters also clears the search. Matching is fuzzy over titles, IDs and body text, so broad words can match many cards, exactly as the sidebar ranking does.
<!-- SECTION:FINAL_SUMMARY:END -->
