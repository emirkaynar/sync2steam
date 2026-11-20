# Synthetic reconciliation fixtures

Specification fixtures for the future offline runner. All IDs and titles below are invented. Load each row in isolation into two temporary profile roots P and Q. The default profile is P with GOG > Epic > Xbox, installed-first enabled, and no previous output unless stated. G/E/X are store names, S is a native Steam AppID, B is a managed binding in P. A verified anchor means an audited same-PC-edition relationship. Re-run each successful plan after applying to a temporary tree and expect zero writes. Repeat with shuffled input order.

| Case | Input or successive observations | Expected output and durable state |
|---|---|---|
| Same edition | G:g1 and E:e1, two purchases, same verified standard anchor, only e1 installed | One group, two entitlements, dispatch to e1. Disable installed-first and g1 install redirect wins if available. B and artwork paths stay constant. |
| Ambiguous name | G:g2 and E:e2 both “North”, no IDs in common, identical publisher | Two groups; review candidate. Never-merge persists after rename and provider refresh. |
| Different editions | G:g3 “North Complete”, E:e3 “North Standard”, same parent, different content | Two outputs if selected. Manual merge preserves SKU metadata. Undo restores both groups and previews any retired binding. |
| Incompatible kinds | Original, remake, remaster, compilation, DLC and pass under one title | Distinct launchable releases; nonlaunchable items have no output. No transitive bridge across a shared art ID. |
| Incomplete inventory | G page 2 times out after page 1, previously seen g9 absent | g9 stays last-known/stale. No revoke/delete. A late response for a disconnected account is discarded. |
| Forced copy | B forced to E:e4, local G:g4 works; E disconnects | Warning and retained pin. One-time fallback uses G only with configured consent. Reorder stores, reconnect E, same B and pin. |
| Install removed | Last local installation removed for owned G:g5 | Ownership unchanged; validated install redirect or explicit unavailable action; change optional status collection on next sync. |
| Installed-only | X:x1 registered locally, no verified account grant | Installed/ownership unknown, never promoted to purchased or included in owned-copy count. |
| Subscription | X:x2 grant expires, E:e6 is permanent, prior pin to X | Expiry changes entitlement eligibility; pin and existing shortcut survive until a deliberate decision. |
| Native and manual Steam | Verified G:g7 ↔ S:700 standard install. Manual shortcut same title in P. Q shares library but applicability unknown | Suppress redundant creation in P when applicable. Q needs review. Explicit import creates separate managed entry if collision-free. Native and manual bytes untouched. |
| Provider correction | Provider moves E:e1 from anchor A to Z after a manual metadata pin | Review divergence, retain group ID, pin, B and artwork override. |
| Manual split | G:g1 and E:e1 grouped, user requests separate outputs or excludes E | Preview separate bindings or retain one as selected. Never conflate “exclude source”, “never import whole group” and “forget account”. |
| Conflicting evidence | A–B and B–C positive but A–C never-merge | No three-member auto-group. Same result regardless of enumeration order. |
| Equal inputs to shortcut hash | Two distinct works both called “North”, same dispatcher path, distinct arguments | Detect collision before write, preserve existing binding, require verified alternative or leave unresolved. |
| Partial apply | P has a manual entry; stop after first file replacement, then alter target externally | Recover untouched known post-images, refuse to overwrite external edit, show exact conflict; Q unchanged. |

Property oracle: grouping/unmerging preserves the multiset of product and entitlement keys. Unknown observations cannot decrease confirmed ownership. Plan order, bindings and action resolution remain deterministic across process restarts. Compare serialized hashes of every untouched file and opaque record before/after.
