# Working on Sync2Steam

Sync2Steam is a native Windows utility that publishes selected external games as Steam shortcuts on demand. Start with the relevant milestone in the [roadmap](docs/roadmap.md). Read the references that apply to the task.

## References by task

- Core and storage · [Architecture decisions](.agents/architecture/decisions.md)
- Steam writes, launch and artwork · [Steam contract](.agents/architecture/steam-and-artwork.md)
- Stores · [Connector guide](docs/contributing/adding-a-store.md)
- Matching and source selection · [Identity contract](.agents/architecture/game-identity-and-duplicates.md) and [test cases](.agents/testing/duplicate-fixtures.md)
- Desktop UI · [UI standards](.agents/standards/ui.md)
- Code, security and testing · [Engineering standards](.agents/standards/engineering.md)

Agent references and source research live in `.agents/`. Contributor guides and the roadmap live in `docs/`. The project uses the [MIT License](LICENSE).

## Guardrails

- Keep ownership, installation state, native Steam apps and managed shortcuts distinct. Only the Steam adapter writes approved changes to managed entries. Require preview, profile validation, backups, verification and conflict-aware recovery.
- Test with synthetic Steam roots and mock accounts. Keep secrets out of logs and fixtures. Verify changing APIs against pinned sources and distinguish tested behavior from proposals.
- Keep the core headless-capable. Use a native UI. Steam login, client patches, persistent services, default telemetry and hidden autostart are out of scope.
