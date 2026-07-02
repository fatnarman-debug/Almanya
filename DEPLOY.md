# Deploy & Production-Checkliste — Aachener Zaun Center

Aktueller Stand: statische HTML-Seiten (index, ueber, zaeune, tore, sichtschutz, kontakt)
mit **Tailwind Play CDN** + Inline-Config. Das ist gut für die Vorschau, aber **nicht für
Produktion** (langsam, FOUC, JIT im Browser). Vor dem Livegang die folgenden Schritte.

---

## 1. Tailwind für Produktion bauen (CDN → statisches CSS)

Der CDN-Ansatz muss durch eine einmal gebaute, minifizierte CSS-Datei ersetzt werden.

```bash
# im Projektordner
npm init -y
npm install -D tailwindcss @tailwindcss/forms @tailwindcss/container-queries

# tailwind.config.js anlegen (Inhalt = der Inline-Config-Block aus <script id="tailwind-config">)
#   content: ["./*.html"]
#   + theme.extend { colors, spacing, fontFamily, fontSize, borderRadius } (1:1 übernehmen)
#   + plugins: [require('@tailwindcss/forms'), require('@tailwindcss/container-queries')]

# input.css anlegen:
#   @tailwind base; @tailwind components; @tailwind utilities;

# CSS bauen (minifiziert)
npx tailwindcss -i input.css -o assets/tailwind.css --minify
```

Dann in **allen 6 HTML-Dateien** ersetzen:
- `<script src="https://cdn.tailwindcss.com...">` → **entfernen**
- `<script id="tailwind-config">...</script>` → **entfernen**
- Im `<head>` stattdessen: `<link rel="stylesheet" href="assets/tailwind.css"/>`

> Die seiten-spezifischen `<style>`-Blöcke (Pill-Nav-CSS) bleiben wie sie sind.

---

## 2. DSGVO / Rechtliches (Pflicht in Deutschland)

- [ ] **Google Fonts self-hosten** (nicht über fonts.googleapis.com laden — LG München).
      Fonts (Hanken Grotesk, Inter, JetBrains Mono, Outfit, Material Symbols) lokal ablegen
      und per `@font-face` einbinden. Die beiden `<link ...fonts.googleapis.com...>` entfernen.
- [ ] **Google Maps (kontakt.html) mit Consent laden** ("2-Klick"-Lösung): Karte erst nach
      Klick/Zustimmung einbetten, oder statisches Kartenbild + „Route planen"-Link. Aktuell
      lädt das iframe Google beim Seitenaufruf → abmahnbar.
- [ ] **Impressum & Datenschutz** müssen auf der Live-Domain erreichbar sein. Die Links zeigen
      auf `https://aachenerzauncenter.de/impressum/` und `/datenschutz/`. Wenn die Seiten dort
      existieren: OK. Wenn nicht: als `impressum.html` / `datenschutz.html` lokal anlegen und
      die Footer-/Formular-Links darauf umbiegen. **Fehlendes Impressum = §5 DDG-Verstoß.**
- [ ] Im Kontaktformular (kontakt.html) ist ein Datenschutz-Hinweis vorhanden. Für index-CTA
      führt jetzt alles auf kontakt.html — passt. Ideal: zusätzlich eine ankreuzbare
      Einwilligungs-Checkbox statt nur Text-Hinweis.
- [ ] Cookie/Consent-Banner nur nötig, sobald Analytics/Tracking/Maps ohne Consent geladen wird.

---

## 3. Bilder lokalisieren (wichtig — sonst brechen sie)

Viele Bilder nutzen temporäre `lh3.googleusercontent.com/aida/...`-URLs (vom Design-Tool).
Diese sind **kurzlebig** und können ausfallen. Vor dem Livegang:
- [ ] Echte Projektfotos in `images/` ablegen (WebP, sinnvoll skaliert), die `lh3...`-URLs
      in index.html, tore.html, sichtschutz.html, zaeune.html ersetzen.
- [ ] `width`/`height` (oder `aspect-ratio`) an `<img>` setzen → verhindert Layout-Shift (CLS).
- [ ] `og:image` pro Seite auf ein echtes, hochgeladenes Bild (min. 1200×630) setzen.

---

## 4. Formular funktionsfähig machen (P0 — separat)

Das Formular in kontakt.html hat aktuell `onsubmit="return false;"` → **es sendet nichts**.
Anbinden an einen Dienst (kein Backend nötig):
- **Formspree** (`https://formspree.io`) oder **Web3Forms** (`https://web3forms.com`) — beide
  DSGVO-tauglich konfigurierbar, kostenloser Einstieg.
- `<form action="https://formspree.io/f/XXXX" method="POST">` + Erfolgs-/Fehler-Feedback
  (`aria-live="polite"`).

---

## 5. Auf die Domain deployen

- [ ] Alle Dateien in das Web-Root von `aachenerzauncenter.de` hochladen.
- [ ] `robots.txt` und `sitemap.xml` müssen unter der Wurzel erreichbar sein
      (`https://aachenerzauncenter.de/robots.txt`, `.../sitemap.xml`).
- [ ] **URL-Struktur entscheiden:** Canonical/Sitemap gehen aktuell von sauberen URLs aus
      (`/zaeune/`, `/kontakt/`, `/ueber-uns/`). Wenn mit `.html` veröffentlicht wird, entweder:
      (a) Server-Rewrite `.html` → saubere URL, **oder**
      (b) Canonical- und Sitemap-Werte auf `.html` umstellen (z. B. `zaeune.html`).
      Sonst stimmen Canonical und echte URL nicht überein.
- [ ] Interne Links sind aktuell `*.html` (funktioniert direkt). Bei sauberen URLs entsprechend
      anpassen.

---

## 6. Nach dem Livegang

- [ ] **Google Search Console**: Domain verifizieren, `sitemap.xml` einreichen, Indexierung anfragen.
- [ ] **Google Business Profile**: NAP exakt wie auf der Seite (Wolfstr. 2, 52134 Herzogenrath,
      +4915754722165), Kategorie „Zaunbauunternehmen", Fotos, und die Google-Bewertungen
      auf der Seite verlinken.
- [ ] `JSON-LD`-Feld `image` (azc.webp) prüfen, dass die Datei live existiert.
- [ ] Optional Analytics (consent-konform) einbauen, um zu messen, welche CTA konvertiert.

---

## Offene fachliche Fragen (bitte bestätigen)

1. **Öffnungszeiten** (kontakt.html: Mo–Fr 08–18, Sa 09–14) — real?
2. **Jahre Erfahrung**: sitewide auf „10+" vereinheitlicht — korrekt, oder anderer Wert?
3. Angaben wie „Meisterbetrieb", „5.0 / 252+ Bewertungen" — belegbar? (AggregateRating-Schema
   nur mit echten, verifizierbaren Google-Daten ergänzen.)
4. **„AC Zaun-Center" (753 Trusted-Shops-Bewertungen)** = dieselbe Firma oder separater Shop?
