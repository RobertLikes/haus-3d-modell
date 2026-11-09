# Übergabe an den Agenten: Einrichtungsmodus

Stand: 09.11.2026. Bitte zuerst `CLAUDE.md` und `docs/uebergabe.md` lesen.

## Worum es geht

Robert hat ein interaktives 3D-Modell seines Hauses in Walluf (Mühlstr. 46, Altbau von 1940) gebaut.
Es zeigt Keller, EG, OG, DG, Fassade, Hof und Garten. Ansehen kann man es auf Android-Smartphone und
in Chrome unter Windows. Bisher wurde es in vielen Chat-Runden als eine einzige HTML-Datei
weiterentwickelt (v30–v82). Seit v82 liegt es in diesem Repository, aufgeteilt in `src/`, und wird in
VS Code weiterbearbeitet.

**Roberts Ziel:** Er will das Modell nutzen, um die Räume **einzurichten und umzugestalten**: Farben
ausprobieren, Möbel umstellen, Varianten vergleichen und dabei echte Maße im Blick behalten. Das Modell
soll also vom reinen Anschauen zu einem einfachen Einrichtungswerkzeug werden. Es muss auf dem Handy
(Touch) genauso gut bedienbar sein wie am PC.

## Gewünschte Funktionen (Reihenfolge = Vorschlag)

1. **Wandfarben je Raum/Wand.** Eine Wand antippen, Farbe aus einer Palette wählen (gängige
   Wandfarbtöne plus freie Farbwahl). Optional: „ganzer Raum“. Zurücksetzen auf Original.
2. **Möbel verschieben und drehen**, wie es heute schon für den Esstisch geht (siehe unten). Das soll für
   alle Möbel gelten: am Boden ziehen, in 15°-Schritten drehen, innerhalb des Raums begrenzt.
3. **Varianten speichern.** Den aktuellen Einrichtungsstand (Farben, Möbelpositionen) unter einem Namen
   speichern, laden und löschen. Erst einmal reicht `localStorage`, zusätzlich Export/Import als
   JSON-Datei, damit Varianten zwischen Handy und PC wandern können.
4. **Maßband.** Zwei Punkte antippen, Abstand in Metern anzeigen. Gedacht für „passt das Sofa hierhin?“.
5. **Möbelkatalog.** Einfache, parametrische Möbel (Sofa, Sessel, Regal, Bett, Tisch, Teppich …) mit
   einstellbaren Maßen in einen Raum setzen.

Alles zusammen gehört in einen **Einrichtungsmodus**: ein eigener Knopf im Dock, der die zugehörigen
Werkzeuge einblendet. Außerhalb des Modus soll sich das Modell verhalten wie bisher: Klick öffnet Türen
und Fenster, Doppelklick wechselt den Raum.

## Was es im Code schon gibt (`src/main.js`)

- **Esstisch verschieben/drehen** (ab ca. Zeile 2220, `// ---- Esstisch verschieben/drehen ----`):
  Raycast auf die Meshes der Gruppe `diningSet`, Ziehen auf einer Bodenebene, Begrenzung per
  `clampX/clampZ`, Drehen mit Knöpfen `rotL/rotR` bzw. Tasten Q/E, Auswahlring, Werkzeugleiste
  `#setTools` in `index.html`. Das ist die Vorlage für Punkt 2.
- **Möbel aus** (`buildFurn()`, ca. Zeile 2256): erkennt Möbel **heuristisch** über Größe und Material.
  Möbel sind meist keine eigenen Objekte, sondern lose Meshes. Für Punkt 2 müssen Möbel deshalb
  zuerst zu benannten Gruppen zusammengefasst werden (je Möbelstück eine `THREE.Group` mit Name,
  Raum und Begrenzung). Am besten dort, wo sie im Code entstehen.
- **Wände:** Fast alle Wände nutzen ein gemeinsames Material `wallMat`. Für Farben je Wand oder Raum
  braucht jede Wand oder jeder Raum ein eigenes Material (z. B. `Mu()` = ungecacht) und eine Zuordnung
  Wand → Raum. Wände sind als `wallMeshes` gesammelt („Wände aus“).
- **Räume:** `ROOMS` (ca. Zeile 2376) enthält die Doppelklick-Räume mit Begrenzungen. Das ist eine gute
  Grundlage für die Zuordnung Wand/Möbel → Raum.
- **Performance-Zusammenfassung** am Ende von `main.js`: statische Meshes werden nach Material zu
  wenigen großen Meshes verschmolzen. Ausgenommen ist alles in `fixed` (aktuell u. a. `acts`, `doors`,
  `sashes`, `diningSet`). **Wichtig:** Alles, was einfärbbar oder verschiebbar werden soll, muss dort
  ausgenommen werden oder ein eigenes Material behalten, sonst ist es nach dem Zusammenfassen
  nicht mehr einzeln greifbar. Gleichzeitig soll die Performance auf dem Handy nicht spürbar leiden.

## Arbeitsweise

- In kleinen, einzeln testbaren Schritten arbeiten. Nach jedem Schritt committen und pushen
  (Repo: https://github.com/RobertTrebor/haus-3d-modell).
- Ansehen: VS Code → Live Server („Go Live“, Wurzel ist `src/`) oder `python -m http.server 8000 -d src`.
- Vor jedem Commit prüfen: Die Seite lädt ohne Konsolenfehler, die bestehenden Ansichten und Klicks
  (Türen, Fenster, Rollläden, Garage, Etagenknöpfe) funktionieren weiter.
- Versionen: Nach größeren Schritten `python build.py v83_Kurzname` (Windows ggf. `py build.py …`).
  Die Datei aus `dist/` kann Robert in einem claude.ai-Chat ins Live-Artifact übernehmen lassen.
- Sichtbare Texte in der Oberfläche auf Deutsch.
- Maße von Robert stehen in `docs/uebergabe.md` und haben Vorrang vor Schätzungen.
- Später, nicht jetzt: Three.js-Update (aktuell r128 per CDN) und Aufteilung von `main.js` in Module.
  Wenn es für den Einrichtungsmodus hilft, darf der neue Code schon in eine eigene Datei
  (z. B. `src/einrichten.js`). Dann muss `build.py` diese Datei mit einbetten.

## Erster Schritt (Vorschlag)

Wandfarben im Wohnzimmer (EG): Den Wohnzimmerwänden eigene Materialien geben, sie vom Zusammenfassen
ausnehmen, einen Knopf „Einrichten“ ins Dock setzen und im Einrichtungsmodus per Antippen einer Wand eine
kleine Farbpalette zeigen. Die Wahl in `localStorage` merken. Danach mit Robert abstimmen, bevor es auf
alle Räume ausgeweitet wird.
