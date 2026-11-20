# Implementation roadmap

The first usable release imports installed games. Its acceptance case is one local game discovered, previewed, published to a selected disposable Steam profile, launched from Steam with Sync2Steam closed, synced again with zero writes, and deliberately removed. Unrelated entries and custom artwork must survive the entire cycle. All milestones below are planned.

## Windows UI gate

Build one GPUI Kit screen with a searchable virtualized list, expandable source rows, selections, local thumbnails and a synthetic sync preview. Exercise Base primitives and selective Component widgets on that screen. Use the reference hardware, fixtures, DPI settings and performance budgets in the [UI standards](../.agents/standards/ui.md).

Pass when a standard user can launch the locked release package on clean Windows 11 x64 without developer tools, complete search → expand → select → preview by keyboard and Narrator, and move between display scales without losing focus, clipping controls or selecting the wrong row. Run the scroll benchmark with seeded thumbnails, including a cover-grid trial, and record startup, search, frame intervals and idle usage against the agreed budgets. A headless command must exit without opening a window. Record compiler, features, lockfile, OS, GPU, runtime files and results. A missing measurement leaves that check open.

If GPUI misses a required check, record the failure and run the same screen, fixture and acceptance checks with egui before selecting a production toolkit. Both trials use the same headless core. Keep the trial screen disposable.

## Installed-game release

| Milestone | Deliverable and acceptance |
|---|---|
| 1. Foundation | Establish the workspace and Windows CI, run the UI gate and package the passing build. Keep normal tests offline. |
| 2. Read-only discovery and preview | Detect a local Epic install and validate its launch action. Discover Steam roots and profiles, parse shortcuts and native manifests, then preview a single addition. Exercise multiple profiles, collision handling, malformed inputs, Unicode paths and unknown supported fields using synthetic fixtures. An installation carries no automatic purchase claim. |
| 3. Safe write and first usable release | With Steam closed, back up the selected disposable profile, publish the approved binding, verify it, and complete the acceptance case above. Test stale previews, a competing writer, failure after each replacement, recovery after restart and refusal to overwrite later user edits. Record the tested Steam and launcher builds. Ship only the verified local action and profile formats. |

## Early feasibility experiments

Begin the Epic and GOG investigations independently alongside local discovery. Run the collection experiment as soon as a disposable shortcut and restorable profile are available. None is a prerequisite for the installed-game release.

| Capability | Evidence required before implementing or advertising it |
|---|---|
| Epic account library | Establish a permitted client identity and auth flow. With consent, verify refresh, all-page traversal for a defined scope and a known owned-but-uninstalled PC title. Exercise expired auth, a failed page and disconnect with fixtures. Record exclusions such as externally fulfilled titles. |
| GOG account library | Independently establish a permitted sign-in and refresh route. With consent, verify pagination and a known uninstalled product. Test coverage of hidden products, extras and editions and record filtering limits. Preserve the last successful snapshot on an interrupted refresh. |
| Steam install-state collections | Back up an isolated Steam profile with an unrelated user collection. With Steam closed, assign one synthetic shortcut to a dedicated static collection. Verify membership and the unrelated collection after restart and cloud reconnect, then restore. If the check fails, expose installation state inside Sync2Steam and leave collection writes disabled for that layout. [Format evidence](../.agents/research/steam.md) |

## Expansion after the installed-game release

1. **Additional local sources and duplicates.** Add GOG installation discovery, then test source grouping, native Steam avoidance, pinned store choice and reversible merge/split across two connectors. Replay the [duplicate fixtures](../.agents/testing/duplicate-fixtures.md) in shuffled order and after restart. A preferred-store change must preserve the existing binding. Cover local Xbox package discovery independently and label title history as activity.
2. **Uninstalled games, one store at a time.** Promote Epic and GOG separately when each account experiment passes. Verify an install or product-page handoff for the selected source, keep stale inventory visible offline and document the tested scope. Verify dispatcher behavior after relocation and across direct-child and URI launches before claiming Input, overlay or lifetime support.
3. **Artwork and optional collections.** Validate all five art slots on a disposable client. Preserve manual overrides, tolerate missing art or throttling and measure cache usage. Implement optional collection writes only after the early experiment passes. Test editable names, external edits, second-client divergence and unsupported layouts before enabling them.
4. **Distribution.** Exercise upgrade, offline use, missing launchers, relocation and uninstall on clean Windows. Publish accessibility and performance results, recovery instructions and third-party notices. The project uses the [MIT License](../LICENSE). The maintainer still needs to confirm public branding before distribution.
