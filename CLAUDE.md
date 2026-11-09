# Hinweise für Claude

- Antworten auf Deutsch. Robert nutzt Android-Smartphone und Chrome unter Windows.
- Maße von Robert haben Vorrang vor Schätzungen aus Fotos; Geschätztes immer als geschätzt kennzeichnen.
  Gesammelte Maße: `docs/uebergabe.md`.
- Gearbeitet wird in `src/`. `dist/` ist generiert (`python build.py`) und nicht im Repo.
- Nach jeder größeren Änderung: Commit mit aussagekräftiger Nachricht, `python build.py vNN_Kurzname`,
  Ein-Datei-Version ans Artifact https://claude.ai/artifact/FohmSLB5zU4L9VUaXLkvSj veröffentlichen.
  Versionsnummern laufen ab v83 weiter (v30–v82 liegen in `Dokumente\Grundriss-Versionen`).
- `main.js` ist eine IIFE. Hilfsfunktionen: `B(x0,x1,y0,y1,z0,z1,mat,parent)` (Quader), `cyl`, `blob`,
  `M(hex,rough,extra)` (Material, gecacht; `Mu` ungecacht), `canvasTex`, `win`, `sash`, `openWin`,
  `swing`, `tog`, `anim`, `clickable`, `deco`, `label`.
- Sichtbarkeitsgruppen: `gCel` (Keller), `gRoof` (Außen/Fassade), `gOG`, `gDG`; Steuerung in `loop()`.
- Ansichten: `VIEWS{key:{p,t,fl,facade}}` + `<option>` in `index.html`; Doppelklick-Räume in `ROOMS`,
  Treppen in `FLIGHTS`; Etagen `setLevel(-1..2)`.
- Performance: statische Teile werden am Ende von `main.js` zu wenigen Meshes zusammengefasst.
  Neue bewegliche oder verschiebbare Teile müssen per `clickable`/`anim`/`doors`/`sashes` registriert
  sein (bzw. vom Zusammenfassen ausgenommen werden), sonst sind sie danach nicht mehr einzeln greifbar.
- Bilder: Datei nach `src/assets/art/`, Eintrag in `src/art.js`, im Code `window.ART.<schlüssel>`.
