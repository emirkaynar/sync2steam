# Contributing to Sync2Steam

The repository currently contains design documents and synthetic test specifications. Documentation corrections are welcome. For an integration proposal, identify the store, exact launcher/client versions, supported capability and a primary source or sanitized reproducible observation. [Adding a store](docs/contributing/adding-a-store.md)

## Development setup for the planned workspace

Target Windows 11 x64 with Rust MSVC via rustup, Visual Studio 2022 Build Tools with Desktop development with C++, Windows SDK, Git and CMake. Start with a stable compiler at least 1.92 for the GPUI spike; record and pin the exact compiler that resolves and builds the full lockfile. A tested minimum version will be published after the first milestone. No Steam installation or store credentials should be prerequisites for normal tests. [Windows feasibility](.agents/research/gpui.md)

After the workspace is introduced, these are the **provisional** local checks:

```powershell
cargo fmt --all -- --check
cargo clippy --workspace --all-targets --locked -- -D warnings
cargo test --workspace --locked
cargo build --release --locked -p sync2steam-app
```

The planned output binary is sync2steam.exe (CLI command sync2steam). Inspect the proposed workspace roles, dependency boundaries, licenses and storage ownership before adding a dependency. Architecture, source research, engineering standards and test specifications live under .agents and are indexed by AGENTS.md. The contributor guides and roadmap live under docs. [Architecture decisions](.agents/architecture/decisions.md) · [Agent guide](AGENTS.md)

## Change and review

Work on a topic branch and keep each PR independently reviewable. State the user-visible behavior, relevant milestone, evidence and limitations. Include tests with fabricated IDs, fixtures for old/new formats, and a short description of how failure or cancellation behaves. Explain any storage migration or newly required permissions. For UI changes include keyboard, DPI and accessibility evidence. Keep the connector capability matrix, user-visible limitations and affected architecture pages current.

For a bug report include app, Windows, Steam/launcher versions and channel, affected operation and expected/observed outcome. Reproduce against an isolated root if possible. Never attach real shortcut databases, account records, game histories, tokens, cookies or raw HTTP traffic. Report a sensitive issue privately to the maintainers using the repository's private reporting channel if enabled; otherwise request a private contact without publishing sensitive details.

Use temporary directories for all writer tests. Do not point fixture builds at your daily Steam profile. A newly observed file format must be safely readable before enabling writes. Record the license and upstream revision before proposing reuse. Check that the intended use and distribution comply with the upstream terms and retain required notices. [Engineering and test standards](.agents/standards/engineering.md) · [Project license](LICENSE)
