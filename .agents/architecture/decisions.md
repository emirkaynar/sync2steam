# Architecture decisions

Accepted design direction for the first implementation, 2026-11-21. Dependency versions are research candidates. Client compatibility, performance and packaging require experiments. Record a superseding decision with evidence when a gate fails.

## 1. A headless core and three adapters

Plan a single Cargo workspace with four packages: sync2steam-core (domain, connector ports, matching, planner and application use cases), sync2steam-stores (one built-in module per store), sync2steam-steam (read/plan/apply, no store imports), and sync2steam-app (Windows composition root, SQLite, network/artwork services, UI and executable). Adapters depend on core. Core never depends on GPUI, SQL, network clients or Win32. The app wires concrete services into use cases; core consumes typed observations and ports. Module boundaries suffice for persistence and artwork initially. Split packages only for independent dependency/build or platform needs.

One sync2steam.exe can parse command mode before initializing GPUI. Proposed commands are interactive UI, dry-run and dispatch by opaque binding ID. Standard output and exit status should work headlessly; validate GUI-subsystem console behavior on Windows. A separate CLI binary or per-store crates add packaging/registration work with little initial isolation. A Rust dynamic ABI needs versioning and loads third-party code with user privileges. A process protocol offers crash isolation but introduces packaging, timeouts and protocol compatibility. Reconsider external workers for incompatible runtimes, not ordinary contributions.

## 2. Separate observation, decision and effect

Connectors enumerate scoped pages, never edit Steam. Complete means all pages within a declared account, platform, product filter and API view succeeded. Record refresh generation, account identity, scope and completeness. Stage pages and publish a generation in a transaction. A newer generation wins over stale jobs and disconnect invalidates in-flight sessions. Verified grants, reported catalog items, activity history and local installs are different evidence. Preserve last-known-good observations on failure. Even absence from a complete filtered view means missing from that scope; require explicit negative evidence before marking revoked. Date subscription expiry with provenance. Neither expiry nor uninstall implies removal.

Presentation uses cached local observations first. A use case requests bounded asynchronous refresh, collects new evidence, runs central matching and produces a per-profile immutable plan of row-level operations and reasons. Compare current, desired and last-applied state. Preserve external edits by default. A conflict or a changed file/revision invalidates its apply preconditions. Explicit deselection previews removal of owned outputs and tracked assets only. A disconnected store stays visible as stale until the user chooses what to keep, forget or remove. A deleted shortcut should present restore/forget options on next preview. [Connector contract](../../docs/contributing/adding-a-store.md) · [Identity](game-identity-and-duplicates.md) · [Writer contract](steam-and-artwork.md)

## 3. Local SQLite plus OS credentials and disposable artwork cache

Use a per-Windows-user data directory, independent of executable location. Portable distribution means a movable binary; secrets and state remain scoped to the Windows user. SQLite transactions can commit a group edit, binding change, override revision and corresponding history together. Uniqueness constraints prevent two publications owning one profile/AppID. Indexed joins support many entitlements and sparse overrides without rewriting an entire inventory. A single JSON/TOML file is attractive for small preferences but needs custom locking, indexing and multi-record recovery here. Keep settings in SQLite, avoid two writable authorities. Prefer a serialized writer, short transactions, explicit busy timeout and read snapshots for dispatch. Do not hold a DB transaction across browser login, HTTP, or user review. [SQLite commit behavior](https://www.sqlite.org/atomiccommit.html)

| Data | Authority and lifetime |
|---|---|
| Groups, revisioned user overrides, exclusions, metadata corrections, selected artwork, binding IDs, before/after ownership | Durable local decisions. Preserve on cache clear, refresh, disconnect and migration. Partition publication preferences by profile. |
| Account metadata, scoped last successful inventory and installation observations | Durable evidence with timestamps and stale flag. Store minimal opaque identifiers. Purge personal evidence on explicit forget after resolving references to managed output. |
| Sync intent, per-file expected and applied fingerprints, backup locations | Durable journal and verifiable backups. Keep until recovery resolved; bound completed history by age/count with user-visible retention. |
| Catalog descriptions, search suggestions, downloaded thumbnails and images | Rebuildable. Store binary assets by digest outside DB, with a size-bounded LRU index. Pin in-use overrides. User originals and recovery backups excluded from eviction. |
| Access/refresh tokens and artwork key | Windows Credential Manager target scoped to application, connector and opaque account. Prefer short-lived tokens in memory. Store only a reference in SQLite. |

Enable foreign keys per connection. Check schema version before writing, refuse unknown newer versions, back up and migrate monotonically in a transaction; test each supported prior schema and interrupted migration. A database transaction cannot include Steam files. Keep a separately recoverable intent and backups before publishing effects. Lost DB or ownership ledger means read-only discovery and explicit recovery, never claiming all matching shortcuts.

## 4. Dependency shortlist

Verified registry metadata retrieved 2026-11-21. These published versions have independent MSRVs; this table is not a tested combination. Resolve one locked graph with the selected GPUI backend and check transitive licenses, compiler requirements and native DLLs on a clean Windows machine. [GPUI dependency matrix](../research/gpui.md)

| Candidate | Declared minimum Rust | License | Justification and restraint |
|---|---|---|---|
| [tokio 1.54.1](https://crates.io/crates/tokio/1.54.1) | 1.85 | MIT | One executor for cancellable HTTP, timers and bounded concurrency if using async Reqwest. Start on demand. Keep GPUI updates on its executor. |
| [reqwest 0.13.5](https://crates.io/crates/reqwest/0.13.5) | 1.85 | MIT or Apache-2.0 | Shared TLS connection pool, explicit timeouts, limited redirects, bounded streaming. Select features for actual codecs/TLS. |
| [serde 1.0.229](https://crates.io/crates/serde/1.0.229) | 1.56 | MIT or Apache-2.0 | Deserialize bounded provider DTOs then validate into domain types. Avoid using DTOs as persistent identity. |
| [windows 0.62.2](https://crates.io/crates/windows/0.62.2) | 1.82 | MIT or Apache-2.0 | Narrow Win32/WinRT features for credentials, packages, process checks and replacement. Isolate FFI in adapters. |
| [tracing 0.1.44](https://crates.io/crates/tracing/0.1.44) | 1.65 | MIT | Local structured operation IDs, outcome and latency. Allowlist fields. |
| [rusqlite 0.43.0](https://crates.io/crates/rusqlite/0.43.0) | 1.89 | MIT | Small synchronous storage adapter off UI thread. Evaluate bundled SQLite versus DLL deployment and update responsibility. |

## 5. Release identity

Keep Sync2Steam provisional. Similar utilities include SteamAll and NoSteam2Steam. A public exact-name search did not establish exclusive availability. Steam is Valve's trademark; inclusion in the application name remains a release risk even with original artwork. The project uses the [MIT License](../../LICENSE). Before public branding, the maintainer decides whether to obtain name clearance or rename, checking relevant jurisdictions, repository/package handles and domain registration at that time. No domain ownership or clearance established. Treat upstream code licenses, API terms and individual art rights separately. [Valve branding](https://partner.steamgames.com/doc/marketing/branding) · [NoSteam2Steam](https://github.com/JesusSolisOrtega/NoSteam2Steam) · [SteamAll](https://github.com/Koisuji02/SteamAll)
