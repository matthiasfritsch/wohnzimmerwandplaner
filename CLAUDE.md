# Wohnzimmer-Wandplaner

Kontext für Claude Code. Entstanden in einer Cowork-Session, wird hier weiterentwickelt.

## Wer & wie

- Nutzer: Matthias, Product Design Lead (Design System, DesignOps). Versteht Code, ist aber kein Dev. Code-Änderungen kurz und für Designer verständlich erklären.
- Entschieden: kein Passwortschutz (statische Seite, Daten liegen eh nur lokal), "Yuna Portrait" bleibt so.
- Sprache: Deutsch, du-Form, knapp, gern mit etwas Humor. Keine Gedankenstriche als Satzzeichen.
- Arbeitsweise: Bei größeren Features erst kurz den Ansatz vorschlagen, bei kleinen Fixes direkt umsetzen. Nach jeder Änderung testen (siehe unten), committen und **direkt nach `main` pushen** (vom Nutzer freigegeben), damit GitHub Pages sich aktualisiert.

## Deployment

- Repo: https://github.com/matthiasfritsch/wohnzimmerwandplaner (public, sonst kein Pages im Free-Plan)
- Live: https://matthiasfritsch.github.io/wohnzimmerwandplaner/
- Pages: Deploy from branch `main`, `/ (root)`
- Alles steckt in einer Datei: `index.html` (HTML + CSS + Vanilla JS, keine Dependencies, kein Build). Das soll so bleiben.

## Was die App ist

Maßstabsgetreuer Planer (alles in cm) für eine Wohnzimmerwand mit Salon-Hängung. Die Wand ist ein SVG, dessen Koordinaten 1:1 Zentimeter sind (x von links, y von oben; "ab Boden" = Wandhöhe minus y).

### Raum & Fixes

- Wand default 600 × 350 cm, in der Kopfzeile einstellbar (Höhenänderung hält Abstand zum Boden).
- Tylko Sideboard (Original Classic, blaue Multiplexplatte, Modell 489 443): 230 × 53 × 40 cm, steht auf dem Boden, horizontal verschiebbar. Gezeichnet mit Türen, Fächern, Schubladen, Sockel. Muss laut Anleitung an die Wand geschraubt werden.
- TV 123 × 70 cm (ca. 55″), Wandmontage, frei positionierbar. Ambilight-Zone: eigener Mindestabstand Kunst zu TV (default 20 cm, Kopfzeile "TV / Ambilight"), gilt rundum inkl. Diagonale, wird als Glow + gestrichelte Linie gezeigt.

### Inventar (aus Google Sheet des Nutzers, im Code als `DEFAULT_TYPES`)

- 52 Rahmen in 27 Größen. Sheet-Logik: `iw/ih` = Bildmaß (sichtbarer Ausschnitt), `w/h` = Außenmaß. Kleinere Zahl = Breite, also default hochkant.
- Fixe Motive: Moosbild (43×53 Holz), Golden Ratio (43,5×69 schwarz), Fisch (21×27 gold), Fisch (18,5×22 grün), Rahmen mit Löchern (25×30 weiß, Form `holes`), Ovaler Rahmen (22×28 weiß, Form `oval`), "Yuna Portrait" (33×33 schwarz).
- 5 Schallplatten 31,5 × 31,5 mit 3D-gedrucktem Halter
- 6 Pflanzenringe 15 × 15 (Topf im Ring, Blätter ragen ca. 18 cm nach oben, zählen nicht zur Fläche, werden aber beim Packing als Luft reserviert)
- IKEA SVARTBJÖRK Spiegel, rund Ø 41 cm, konvex, schwarz (1×)
- Skateboard-Decks 20,5 × 81 cm (8,0″ × 31,8″), Holz oder Griptape, Grafik hochladbar, vertikal/horizontal (2×)
- IKEA SVENSHULT Wandregal 60 × 35 × 20 cm, Drahtgitter, Z/S-Form (Gitterboden in der Mitte, links Platte nach unten, rechts nach oben). Horizontal/vertikal und Z/S spiegelbar, Sprühlack-Farbe wählbar (default Graugrün). Front-Darstellung nach IKEA-Produktbild; Schraubpunkte nicht verifiziert.
- Bestand pro Typ per Klick auf den Zähler im Inventar änderbar. Eigene Objekte über "Eigenes Objekt ergänzen".

### UI-Aufbau

- Kopfzeile: Wandmaße + Farbe, Abstand + Ambilight, Undo/Redo, Speicherstatus (grüner Punkt, Text ab 1280 px), Bohrplan, ⋯-Menü (Neue Wand, Import, Export).
- Schwebende Canvas-Leiste unten rechts (`#canvasbar`, fixed): Snapping/Hilfslinien/Person als Toggle-Pills, Zoom −/+ und Zoom-Anzeige in % relativ zu Fit (Klick = einpassen).

### Interaktion

- Drag & Drop aus Inventar oder +, Mehrfachauswahl (Shift-Klick, Rahmen aufziehen), Pfeiltasten 1/5 cm, R drehen, Entf, Strg+D, Strg+Z/Y, Strg+A.
- Snapping: nur zu Objekten in max. 45 cm Nähe (sonst klebt man fest), Schwelle 5 px, Kanten/Mitten bündig, exakter Abstand zu Nachbarn (am TV Ambilight-Abstand), Wandmitte, Augenhöhe 150 / sitzend ~110. Alt = aus.
- Maßketten bei Auswahl (Abstand links, ab Boden, Größe).
- Rechter Drawer pro Objekt: Motiv hochladen (auf 800 px verkleinert, JPEG) mit Zoom/Verschieben, Passepartout an/aus + Farbe + Ausschnitt frei, Rahmenfarbe, Leistenbreite (geschätzt, max. 2 cm default), Hoch/Quer, Position numerisch, Notiz, Sperren. Bild auch per Drag aus dem Finder direkt auf den Rahmen.

### Komposition (rechtes Panel, zwei Tabs)

- Vorschläge: Auto-Komposition aus freiem Bestand mit Bezug (Salon rund um den TV, Symmetrisch neben TV, Galerie über dem TV, Mittelachse durch den TV, Freie Gruppe), Anzahl, Zufall (ordentlich/locker/wild), Form (Seitenverhältnis), Farbwelten, große Anker, Objekte einmischen. "Überrasch mich" würfelt alles. Vorlagen (Raster 4×3, 3×3, 2×2, Duo, Platten-Reihe/Kreuz, Pflanzen-Reihe/Treppe).
- Meine Auswahl: Nutzer legt fixes Set fest (+ im Inventar legt ins Set), pro Stück Hoch/Quer/Zufall. "Komposition bauen / Neu würfeln / Anderes Layout" ordnet nur neu an, Motive/Passepartouts bleiben. Neue Stücke werden direkt an die bestehende Gruppe angebaut, ohne den Rest zu bewegen (abschaltbar). Bis 8 Varianten merken und per Chip laden. Wand-Auswahl kann per "Als Auswahl übernehmen" zum Set werden.
- Mehrfachauswahl-Aktionen: Salon kompakt (mit TV in Auswahl bleibt TV fix), Raster, Reihen (Mittelachse/Ober-/Unterkante), Spalte, verteilen, ausrichten.
- Checks live: Überlappung, außerhalb, Abstände < Gap, Ambilight-Zone, Luft über Sideboard (15 bis 30 cm), TV-Mitte (100 bis 115 cm), Größe/Schwerpunkt der Hängung.

### Output & Speichern

- Bohrplan: Druckansicht (A4 quer, als PDF speicherbar) mit nummerierter Wand und Tabelle. Nagelhöhe = Oberkante minus Aufhänger-Offset (default 3 cm). Spezifische Regeln für TV, Platten, Pflanzen, Spiegel, Decks (2 Halter), Regal (Endplatten).
- Export/Import als JSON (inkl. Bilder als Data-URL, Set, Varianten). Import ergänzt fehlende neue Default-Typen.
- Autosave in IndexedDB (DB `wandplaner`, Store `kv`, Key `state`, Wert `{t, json}`), alle 1,5 s und beim Tab-Wechsel. Kein 5-MB-Limit mehr. `localStorage` (`wandplaner-v1`) nur noch Fallback, alte Stände ziehen beim ersten Laden automatisch um. Button "Neu" für leere Wand.
- Bilder liegen nur lokal im Browser des jeweiligen Geräts (als JPEG-Data-URL im State), nie auf GitHub. Gerätewechsel oder Backup nur über Export/Import.

## Code-Landkarte (index.html)

- Daten/Zustand: `DEFAULT_TYPES`, `freshState()`, globales `S` (wall, gap, tvGap, sb, types, items, set, variants), `ui` (Tabs, Auto-Einstellungen), `sel` (Auswahl).
- Geometrie: `dims()`, `rectOf()`, `bboxOf()`, `gapPair()` (TV-Abstand), `isTV()`.
- Rendering: `renderStatic` (Wand, Lineale, Hilfslinien), `renderSB`, `renderItems/renderItem/itemMarkup` (pro Objektart), `motif()`, `renderOverlay/renderHalo/renderGuides`.
- Interaktion: Pointer-Handler auf dem SVG, `snapBox()`, Keyboard-Handler, Drop-Handler.
- Layout-Engine: `pack()` (relativ), `packAroundTV()` (echte Wandkoordinaten, TV fix), `symmetricTV()`, `lineTV()`, `gridLayout/rowLayout/colLayout`, `applyLayout(pos, place, settle)`, `layoutGroup()`, `attachItems()` (anbauen), globale `CHAOS` für Zufall.
- Set-Modus: `addToSet`, `buildSet`, `selToSet`, `saveVariant/loadVariant`, `rotFor()` (Hoch/Quer unabhängig von Grundausrichtung).
- Panels: `renderLeft` (Inventar), `renderPanel` → `renderDrawer` / `renderMulti` / `renderSBPanel` / `renderCompose` (+ `setPanel`).
- Undo: `pushHistory(key)` vor jeder Mutation (mit key = Coalescing für Slider), `snapshot/restore`.
- Persistenz: `db()/idb()` (IndexedDB-Helfer), `saveLocal()` (sync aufrufbar, speichert async), `loadLocal()` (async, Init wartet darauf), Export/Import-Handler, `printPlan()`.

## Testen

Chromium + Playwright (Python) headless. In der Cloud-Umgebung: `pip install playwright`, dann mit `p.chromium.launch(executable_path='/opt/pw-browsers/chromium')` starten (nicht `playwright install`). Seite per `file://` laden, Aktionen per `page.evaluate`/Klicks, danach prüfen:

- keine `pageerror`s
- `issues()` liefert `ov`, `out`, `tvTight` = 0 nach Vorschlägen/Set-Builds
- nie mehr Objekte eines Typs als `count`
- Drag mit Snapping bewegt sich weiter (es gab einen Bug, bei dem ein Fehler in `snapBox` das Ziehen blockiert hat)
- Screenshot anschauen, bevor gepusht wird

## Offene Punkte / Ideen

- Steckdosen, Schalter, Fenster als Hindernisse (bewusst zurückgestellt)
- Beim Entfernen aus "Meine Auswahl" bleibt eine Lücke; ob die automatisch geschlossen werden soll, ist noch offen
- SVENSHULT und Spiegel: echte Aufhängepunkte gegen Montageanleitung prüfen
