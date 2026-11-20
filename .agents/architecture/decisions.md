# Architecture blueprint

## Accepted constraints

- Use Rust and keep the core callable without a window. Domain types, matching and planning have no dependency on a UI toolkit, Win32, SQL or an HTTP client.
- Store connectors return scoped observations and typed actions. Preserve their provenance. A failed refresh, missing page or uninstall cannot revoke ownership. Central reconciliation owns grouping and source selection. [Connector guide](../../docs/contributing/adding-a-store.md) · [Identity rules](game-identity-and-duplicates.md)
- Only the Steam adapter applies a previewed plan to verified managed entries in the selected profile. Preserve user edits. Require backups and recoverable writes with Steam closed. An unknown format or lost ownership ledger blocks the affected write. [Steam contract](steam-and-artwork.md)
- Keep binding IDs, overrides and the last successful inventory durable across app restarts. Isolate credentials in current-user OS storage. Cache eviction must preserve user selections, original art and recovery data.

## Working choices

Start with a core, built-in store modules, a Steam adapter and a Windows application that wires them together. Four workspace packages are a candidate layout. Keep the executable usable for on-demand dispatch before UI initialization. Prefer SQLite for transactions spanning groups, overrides and profile bindings. Steam file recovery needs its own verified before and after states outside a database transaction.

## Decisions that need evidence

| Choice | Deciding experiment |
|---|---|
| Native UI and toolchain | Trial GPUI Kit on the representative Windows screen. If it fails a required check, trial egui under the same conditions. Record the compiler, lockfile and runtime files from a passing clean-machine build. [Windows gate](../../docs/roadmap.md#windows-ui-gate) |
| Networking | At the first account or artwork workload, compare a bounded worker pool using blocking ureq with Tokio and Reqwest. Exercise cancellation during slow responses, concurrent requests, UI responsiveness and shutdown. Choose the simplest option that meets the workload. |
| Persistence and recovery | Use the first durable binding and interrupted sync to test uniqueness, restart and restore. Verify SQLite packaging on clean Windows before choosing bundled SQLite or a DLL. Set schema evolution and cache policy when representative data exists. |

A published crate version or upstream implementation is evidence for a trial. Record support only for the combination the project actually validates.
