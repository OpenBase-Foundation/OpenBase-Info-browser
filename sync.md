# Syncing OpenBase patches into a Chromium src tree

This document describes two supported ways to add the OpenBase patches (from this repo) into a Chromium source checkout on Windows.

Prerequisites
- A Chromium checkout at C:\Users\mats\chromium\src
- Python 3 on PATH (invoke as `py -3` or `python`)
- Git installed
- The OpenBase patch set in this repository (patch files under patches/ or patches/extra/openbase)

Option A — Using the repo patch layout (preferred when keeping ungoogled-style patch sets)
1. Copy the OpenBase patch folder into the Chromium `patches/` tree:
   - Create the target folder (example):
     - PowerShell:
       New-Item -ItemType Directory -Force -Path 'C:\Users\mats\chromium\src\patches\extra\openbase'
     - Copy files:
       robocopy "C:\Users\mats\OpenBase-Info-browser\patches" "C:\Users\mats\chromium\src\patches\extra\openbase" /E

2. Add or update a SERIES (or other series files) in the target directory listing the patches in order.
   - Each patch file should be a unified diff (standard .patch).
   - Example SERIES content (one filename per line):
     ```
     0001-branding-openbase.patch
     0002-add-openbase-webui.patch
     ```

3. If your Chromium checkout uses the same devutils helper (`update_platform_patches.py`) you can merge/unmerge platform-style patches:
   - Merge:
     py -3 "C:\Users\mats\OpenBase-Info-browser\devutils\update_platform_patches.py" merge "C:\Users\mats\chromium\src\patches\extra\openbase"
   - Unmerge (to revert):
     py -3 "C:\Users\mats\OpenBase-Info-browser\devutils\update_platform_patches.py" unmerge "C:\Users\mats\chromium\src\patches\extra\openbase"

   Note: `update_platform_patches.py` variants in this repo may expect a particular directory layout. Inspect the script header for expected args and adapt the path accordingly.

4. Run GN/Ninja as usual:
   - Set GN args (example):
     set CHROMIUM_OUT=out\SchoolEdition
     gn gen %CHROMIUM_OUT% --args="is_component_build=false is_debug=false symbol_level=0 chrome_pgo_phase=0"
   - Build:
     autoninja -C %CHROMIUM_OUT% chrome

Option B — Applying patches directly to the Chromium git tree (useful for quick testing)
1. From the Chromium src root:
   cd C:\Users\mats\chromium\src

2. For each patch file from OpenBase-Info-browser:
   - Validate and apply with git am (keeps commit metadata):
     py -3 -c "print('Using python to ensure py available')"
     git am --whitespace=fix "C:\Users\mats\OpenBase-Info-browser\patches\0001-my-patch.patch"
   - Or to test without creating commits:
     git apply --check "C:\Users\mats\OpenBase-Info-browser\patches\0001-my-patch.patch"
     git apply "C:\Users\mats\OpenBase-Info-browser\patches\0001-my-patch.patch"

3. Build (same GN/Ninja commands as above).

Tips and troubleshooting
- If a patch fails to apply cleanly, rebase or port the patch against the Chromium revision you have (use `git apply --reject` or open the .rej).
- Ensure line endings are compatible (use core.autocrlf=false when applying patches that assume LF).
- Keep a copy of the original patch set (do not edit upstream Chromium files until you confirm).
- If the Chromium tree uses a custom patch orchestration, adapt the steps. The included devutils scripts in this repo are a helper; read their docstrings to confirm expected layout.

Theme assets & runtime
- WebUI and theme scaffolding added by OpenBase write user themes into the browser profile:
  %LOCALAPPDATA%\[Profile]\openbase_themes\<theme-id>\{theme.json,theme.css}
- Selected theme persisted at:
  %LOCALAPPDATA%\[Profile]\openbase_selected_theme.json

Reference
- This sync file assumes the OpenBase patches live in this repository under `patches/` or `patches/extra/openbase`. Adjust paths if your layout differs.