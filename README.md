# Sync2Steam

![Phase 0: Research & Planning](https://img.shields.io/badge/Phase_0-Research_%26_Planning-8B5CF6?style=flat&logo=blueprint&logoColor=white&labelColor=20232A)


Sync2Steam is an early-stage, open-source project to bring games from other PC stores into Steam - including installed & **uninstalled** titles. The idea is simple: use Steam as your library, without keeping another library manager running.

## Why I'm building it

I've used [Playnite](https://github.com/JosefNemec/Playnite), and I think it's great at what it does. It pulls scattered libraries together and gives you a proper place to manage them. But I kept coming back to Steam to actually play my games. Eventually, maintaining a second launcher felt like more than I needed.

I wanted something I could open, sync, and close. My games would be waiting in Steam, with the right artwork and a sensible way to get them installed or launched. No extra launcher to live in the tray, and no Steam client mods.

## What I'm aiming for

- **Your whole library, not just installs.** Discover owned games from Epic Games, GOG, Xbox and other game stores, where account integrations allow it.
- **Games that look at home in Steam.** Covers, wide artwork, heroes, logos, and icons from [SteamGridDB](https://www.steamgriddb.com/).
- **One game, multiple stores.** Detect duplicate ownership, keep distinct editions separate, and let you choose which store takes priority.
- **Installed or not?** Organize entries using Steam collections; launch installed games or open their original store to install them when possible.
- **Sync on your terms.** Preview changes, preserve your existing shortcuts and customizations, and close the app when you're done.

The plan is a lightweight Windows app built with Rust and GPUI. Some external stores may require account connections to retrieve uninstalled purchases.

## Why not use something else?

You absolutely should if it already works for you:

- **[Playnite](https://github.com/JosefNemec/Playnite)** is an excellent full-featured library manager. Sync2Steam isn't trying to replace it; Steam *is* the library here.
- **[Steam ROM Manager](https://github.com/SteamGridDB/steam-rom-manager)** is fantastic for bulk-importing games and managing artwork. I'm interested in going further with account libraries and uninstalled titles.
- **[SteamAll](https://github.com/Koisuji02/SteamAll)** explores a similar idea and deserves credit for it. I want to explore broader store support and a solution that doesn't rely on Steam client modifications.

And of course, a lot of the visual side of this idea wouldn't be possible without the people contributing artwork to **[SteamGridDB](https://www.steamgriddb.com/)**.

## Status

**Planning and research.** Sync2Steam isn't ready to use yet. Store support - especially complete Xbox library discovery - and install redirects still need to be researched and tested. The goals above are not claims of working features.

## Disclaimer

Sync2Steam is an independent, unofficial project. It is not affiliated with, endorsed by, or sponsored by Valve Corporation or any of the supported storefronts. Steam is a trademark of Valve Corporation; all other trademarks belong to their respective owners.