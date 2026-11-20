# Working on Sync2Steam

Sync2Steam is a native Windows utility that publishes selected external games as Steam shortcuts on demand. Start with the relevant milestone in the [roadmap](docs/roadmap.md). Read the references that apply to the task.

## References by task

- Core and storage · [Architecture decisions](.agents/architecture/decisions.md)
- Steam writes, launch and artwork · [Steam contract](.agents/architecture/steam-and-artwork.md)
- Stores · [Connector guide](docs/contributing/adding-a-store.md)
- Matching and source selection · [Identity contract](.agents/architecture/game-identity-and-duplicates.md) and [test cases](.agents/testing/duplicate-fixtures.md)
- Desktop UI · [UI standards](.agents/standards/ui.md)
- Code, security and testing · [Engineering standards](.agents/standards/engineering.md)

## Commands once the workspace exists

Provisional Windows checks for the planned workspace:

```powershell
cargo fmt --all -- --check
cargo clippy --workspace --all-targets --locked -- -D warnings
cargo test --workspace --locked
cargo build --release --locked -p sync2steam-app
```

## Completion and uncertainty

A task is done when its scoped behavior works through the intended entry point, applicable checks pass and its roadmap acceptance evidence is recorded. For documentation changes, check links and consistency. Claim client compatibility only after the relevant disposable-client trial passes.

When sources disagree or an API or file format is unclear, pause the affected operation and continue independent work. Add a short spike note to the relevant research page with the conflicting evidence, revisions, smallest safe experiment and outcome or blocker. Never guess a Steam write format or a store authorization flow. Ask the maintainer when a decision changes product scope, requires real account access or risks user data.

## Guardrails

- Keep ownership, installation state, native Steam apps and managed shortcuts distinct. Only the Steam adapter writes approved changes to managed entries. Require preview, profile validation, backups, verification and conflict-aware recovery.
- Test with synthetic Steam roots and mock accounts. Keep secrets out of logs and fixtures. Verify changing APIs against pinned sources and distinguish tested behavior from proposals.
- Keep the core headless-capable. Use a native UI. Steam login, client patches, persistent services, default telemetry and hidden autostart are out of scope.
