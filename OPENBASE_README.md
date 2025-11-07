# OpenBase Browser — School Edition (short project README)

Purpose
- A privacy-focused, school-branded Chromium fork.
- Adds School Edition features: privacy defaults, preinstalled homepage, a WebUI settings page (chrome://openbase), and a runtime theme selector for per-school appearance without recompilation.

Key features added in this repo
- chrome://openbase — small WebUI that lists and applies themes.
- Theme manager (C++) — persists per-profile theme selection and reads theme packages from the profile folder.
- Help menu item that opens the OpenBase settings page.
- Simple theme package format:
  - theme.json (required): { "id": "school-blue", "name": "School Blue" }
  - theme.css (required): CSS that is injected into WebUI pages (and can be extended to other UI parts).

Developer workflow (summary)
1. Prepare/verify patches in this repo under `patches/` or `patches/extra/openbase`.
2. Sync patches into your Chromium checkout. See `sync.md` for step-by-step commands.
3. Build Chromium:
   - GN args example:
     gn gen out/SchoolEdition --args="is_component_build=false is_debug=false symbol_level=0 chrome_pgo_phase=0"
   - Build:
     autoninja -C out/SchoolEdition chrome

Where code lives (high level)
- New native code: chrome/browser/openbase/ (ThemeManager)
- New WebUI: chrome/browser/ui/webui/openbase/ (controller + message handler)
- Web resources: chrome/browser/resources/openbase/ (openbase.html, openbase.js, openbase.css)
- Menu wiring: Browser menu code + a new command id to open chrome://openbase
- Patches: `patches/extra/openbase/` (recommended location in this repo)

Theme author quick start
1. Create a theme folder inside a browser profile:
   - Windows example:
     C:\Users\<user>\AppData\Local\<Profile>\openbase_themes\school-blue\
2. Add files:
   - theme.json
     {
       "id": "school-blue",
       "name": "School Blue",
       "author": "IT Dept",
       "version": "1.0"
     }
   - theme.css — rules that override WebUI CSS variables or selectors.
3. Open chrome://openbase and select the theme from the list.

Testing & iteration
- After syncing patches, build the browser and launch with a test profile.
- Put a sample theme in the test profile folder and open chrome://openbase to verify listing and apply flow.
- For broader UI theming (toolbars, native UI), integrate with Chromium's theme system — the provided ThemeManager is a starting point for WebUI-level theming.

Notes & next steps
- This README is intentionally short. See `sync.md` for concrete patch sync commands.
- To ship themes centrally, create installation scripts that copy theme folders into the default profile templates or deploy via your endpoint management tooling.
- If you want, I can add GRD entries and resource packaging entries so chrome://openbase is bundled into the chrome binary.

Contact / contributions
- Place patch files in `patches/extra/openbase/`, update the SERIES file, and follow `sync.md`.
- For help reproducing build errors, include the build output and the failing patch filenames.

License
- Respect Chromium licensing when merging files into the Chromium tree. This repository is a set of changes on top of Chromium; follow your project's licensing and contributor policies.
