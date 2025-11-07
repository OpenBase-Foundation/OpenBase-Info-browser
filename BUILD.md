🧱 Chromium bouwen in batches met 4 threads
🥇 Stap 1 — Zorg dat GN klaarstaat

Voer eerst nog even GN uit (met PGO uitgeschakeld):

gn gen out\SchoolEdition --args="is_component_build=false is_debug=false symbol_level=0 chrome_pgo_phase=0"

🥈 Stap 2 — Bouw de kernbibliotheken

Dit zet de fundamentele libraries klaar (worden hergebruikt door alles wat volgt):

autoninja -C out\SchoolEdition base -j4
autoninja -C out\SchoolEdition build -j4
autoninja -C out\SchoolEdition net -j4

🥉 Stap 3 — Bouw de componenten en UI

Dit bouwt de modules die Chrome gebruikt, inclusief rendering en mediacomponenten:

autoninja -C out\SchoolEdition components -j4
autoninja -C out\SchoolEdition content -j4
autoninja -C out\SchoolEdition ui -j4

🏗️ Stap 4 — Bouw de Chrome-binaries

Nu de browser zelf (de grote stap):

autoninja -C out\SchoolEdition chrome -j4