# LOUM Design System — Auswertung der awesome-react-Liste (v2)

**Scope:** Alle Abschnitte der [README](README.md) ab **„React Styling"** bis zum Ende (inkl. React Native), bewertet gegen den Stack des loum.ai-Frontends.
**Stand:** 24. August 2026

> **Revision v2:** Die Erstfassung (v1, gemerged in PR #1) basierte auf der Bot-Kopie `loum.ai-spiritual-companion`. Das echte Repo `loum-ai/loum.ai` weicht davon stark ab — diese Fassung basiert auf einer frischen Analyse von dessen `main`-Branch. Ergebnis: **10 der v1-Empfehlungen sind dort bereits umgesetzt**; übrig bleibt eine kleinere, schärfere Shortlist.

## Ist-Stack loum.ai (real, Stand 24.08.2026)

- **Monorepo:** Turborepo + Bun
- **Framework:** TanStack Start (~1.168) + TanStack Router (~1.170), Vite 8, React 19.2 — kein Next.js
- **Styling:** Tailwind CSS 4 (CSS-first), eigenes Token-Package `packages/tokens/src/loum/*.css` (18 Schichten), shadcn/ui auf dem vereinheitlichten `radix-ui`-Paket (1.6.7)
- **State/Daten:** zustand 5; kein TanStack Query (Start-Loader/Server-Functions decken Data-Fetching ab); Supabase (Auth, PostgREST, Edge Functions)
- **AI:** serverseitig Vercel AI SDK `ai@7` + `@ai-sdk/google@4` (gemini-3-flash-preview) — **der Client parst den SSE-Stream aber weiterhin von Hand**
- **Motion:** motion 13 (ehem. framer-motion); kein Three.js mehr
- **Forms/Validation:** kein react-hook-form, kein zod
- **Testing/Doku:** bun test, Playwright (E2E + visuelle Tests), Storybook 10
- **Sonstiges:** i18n (de/en), Deployment auf Cloudflare Workers

Die Bewertung fragt weiterhin: **„Bringt die Bibliothek dem LOUM Design System etwas, das der Stack noch nicht hat — ohne Doppelungen?"**

**Legende:** ✅ übernehmen · 🔁 schon im Stack (mit Empfehlung) · 🧩 bei Bedarf / Feature-getrieben · ❌ nicht übernehmen

---

## Delta: v1-Empfehlung → Status im echten Repo

| v1 empfahl | Status in `loum-ai/loum.ai` |
|---|---|
| Storybook einführen | ✔ läuft bereits (Storybook 10) |
| Vercel AI SDK | ✔ serverseitig (`ai@7` + Google-Provider) — **Client-Hälfte fehlt noch → bleibt Empfehlung #1** |
| Tailwind 4 (CSS-first) | ✔ erledigt, Tokens als eigenes Package mit 18 Schichten |
| react-router 7 | ✔ übererfüllt: TanStack Start + TanStack Router |
| Vite 7 | ✔ Vite 8 |
| framer-motion → motion | ✔ motion 13 |
| React 19 (+ R3F 9) | ✔ React 19.2; Three.js wurde entfernt — R3F damit obsolet |
| Vitest + RTL + Playwright-Smokes | teils: bun test + Playwright (E2E + visuell) laufen; **Komponententests mit RTL bleiben offen** |
| zustand (Randnotiz) | ✔ zustand 5 |
| react-i18next bei Bedarf | ✔ i18n de/en vorhanden |
| recharts-Upgrade | gegenstandslos — im aktuellen Stack ist keine Chart-Bibliothek (mehr) erkennbar |
| Konsolidierung (sonner, lucide, embla) | im Monorepo neu prüfen — v1-Befunde stammen aus der alten Kopie |
| react-scan · react-error-boundary · auto-animate · tanstack-table | ✳ **weiterhin offen — das ist die neue Shortlist** |

---

## Das Beste — Shortlist v2

| # | Bibliothek | Warum für loum.ai |
|---|-----------|-------------------|
| 1 | **ai-sdk, Client-Hälfte** (`@ai-sdk/react`) | Der Server spricht schon AI SDK 7, der Client parst SSE von Hand — die Lücke schließen: `useChat` liefert Streaming, Abbruch, Fehler- und Ladezustände getestet statt handgerollt. Kleinster Eingriff, größter Produkt-Hebel; Provider-Wechsel (Gemini ↔ andere) bleibt dank SDK trivial. |
| 2 | **react-scan** | Dev-only Performance-Scanner ohne Code-Änderung, React-19-tauglich. Für die animationslastige LOUM-UI: macht vermeidbare Re-Renders sichtbar, bevor sie das 60-fps-Gefühl kosten. |
| 3 | **react-error-boundary** | Klein, etabliert. Widgets, Chat und Routen-Inseln einzeln absichern, damit ein Fehler nicht die ganze App weiß rendert; ergänzt die Error-Boundaries von TanStack Router auf Komponenten-Ebene. |
| 4 | **react-testing-library** | Die fehlende Test-Ebene zwischen bun test (Unit) und Playwright (E2E/visuell): Komponenten-Verhalten testen. Läuft mit bun test + happy-dom/jsdom — **kein Vitest nötig**, der Runner bleibt Bun. |
| 5 | **tanstack-table** | Sobald echte Datentabellen kommen: headless, stylt sich über die shadcn-Table-Styles — und passt jetzt doppelt, weil das Repo ohnehin im TanStack-Ökosystem lebt (Start, Router). |
| 6 | **auto-animate** | Zero-Config-Übergänge für Listen (Feeds, Verläufe), wo Motion-Varianten Overkill sind. ~2 kB, eine Zeile pro Container. |

**Feature-Picks** (übernehmen, sobald das Feature ansteht):

- **xyflow (React Flow)** 🧩 — Archetypen-„Konstellation" als interaktiver Graph. Sehr gut gepflegt, Signature-Feature-Potenzial.
- **tanstack-form + zod 4** 🧩 — RHF/zod sind aus dem Stack raus. Wenn komplexe Formulare zurückkommen, ist tanstack-form die ökosystem-konsistente Wahl, zod als Validierung dazu.
- **react-parallax-tilt** 🧩 — Holo-/Tilt-Effekt für Tarot-Karten. Winzig, gepflegt, maximaler Brand-Fit.
- **react-pdf** 🧩 — Readings/Dossiers als gestaltetes PDF exportieren.
- **react-big-calendar** 🧩 — falls der Cosmic Calendar eine klassische Monats-/Wochenansicht braucht; „kosmische" Custom-Darstellungen eher selbst bauen.
- **qrcode.react** 🧩 — Sharing-Features (Reading teilen, Profil-QR). Klein und stabil.
- **remotion** 🧩 — Content-Baustein: Daily-Card-Reels programmatisch rendern. Lizenzmodell beachten.

## Prüfpunkte im Monorepo (aus v1 offen)

Die v1-Konsolidierungsbefunde stammen aus der alten Kopie und sind am echten Repo zu verifizieren — die Regel bleibt: **eine Lösung pro Kategorie.**

1. **Toasts:** sonner ist der shadcn-Standard — prüfen, dass keine zweite Toast-Schicht (alte shadcn `toast.tsx`) mitläuft.
2. **Icons:** eine Icon-Sprache (bei shadcn üblicherweise lucide-react), keine Mischung mit react-icons/heroicons.
3. **Carousel:** falls vorhanden, embla (steckt im shadcn-Carousel) — kein swiper/keen-slider daneben.
4. **Markdown:** falls Markdown gerendert wird, bei react-markdown bleiben; markdown-to-jsx lohnt keinen Wechsel.
5. **Dependenz-Pflege:** TanStack Start/Router bewegen sich schnell — Renovate/Dependabot einrichten, falls nicht vorhanden.

---

## Bewertung Abschnitt für Abschnitt (v2)

### React Styling

- **styled-components** ❌ — offiziell im **Maintenance-Modus** (seit 2025); Runtime-CSS-in-JS widerspricht Tailwind 4.
- **emotion** ❌ — gepflegt, aber ein zweites Styling-Paradigma erzeugt nur Inkonsistenz.
- **vanilla-extract** ❌ — die Tokens liegen bereits als CSS-Schichten im Token-Package; zero-runtime ist mit Tailwind 4 ohnehin gegeben.

> **Fazit Styling:** Nichts übernehmen. Mit Tailwind 4 CSS-first + Token-Package ist loum.ai dem Abschnitt schlicht voraus.

### React Icon Libraries

- **lucide-react** 🔁 — shadcn-Standard; als einzige Icon-Sprache führen (im Monorepo verifizieren).
- **react-icons** ❌ (Stil-Mix), **heroicons** ❌ (redundant), **thesvg** ❌ (Nische).

### React Routing

- **tanstack-router** 🔁 — im Stack (via TanStack Start); aktuell halten.
- **react-router** ❌ — nicht mehr im Stack; kein Grund zur Rückkehr.
- **speedy-router** ❌ — als TanStack-API-Rebuild konzeptionell interessant, aber zu jung für Produktions-Wetten.

### React Development Tools

- **vite** 🔁 — Vite 8, aktuell.
- **react-scan** ✅ — Shortlist #2.
- **why-did-you-render** ❌ — react-scan deckt es weniger invasiv ab.
- **eslint-plugin-react** 🧩 — bei Gelegenheit ergänzen, kein Muss.
- **parcel** ❌, **reactotron** ❌.

### React Libraries

- **ai-sdk** ✅ — Shortlist #1: Server nutzt es schon, die Client-Hälfte (`@ai-sdk/react`) fehlt.
- **react-error-boundary** ✅ — Shortlist #3.
- **floating-ui** ❌ — steckt in Radix unter der Haube.
- **downshift** ❌ — Radix/cmdk decken Combobox-Fälle ab.
- **loadable-components** ❌ — `React.lazy` + Router-Code-Splitting reichen.
- **react-uploady** ❌ — Uploads direkt über Supabase Storage.
- **preact** ❌.

### React Testing

- **react-testing-library** ✅ — Shortlist #4, mit bun test als Runner.
- **playwright** 🔁 — läuft bereits (E2E + visuell).
- **jest** ❌ — bun test ist gesetzt.
- **cypress** ❌ — Playwright ist gesetzt.

### React Awesome Components

- **react-hot-toast** ❌ — sonner ist der shadcn-Standard.
- **kbar** ❌ — cmdk ist der shadcn-Standard für ⌘K.
- **swiper / keen-slider** ❌ — embla, falls Carousel gebraucht wird.
- **react-select** ❌ — Fremd-Look, schwer auf LOUM-Tokens zu trimmen.
- **react-datepicker** ❌ — react-day-picker ist der shadcn-Weg.
- **qrcode.react** 🧩 — Feature-Pick Sharing.
- **react-insta-stories** ❌ — Idee gut (Daily-Card als Story), Pflege mäßig → mit embla + motion selbst bauen.
- **react-big-calendar** 🧩 — Feature-Pick Cosmic Calendar.
- **puck** ❌ — visueller Page-Builder ohne Use-Case.
- **react-archer / react-complex-tree / tagify / heart-switch / json-edit-react** ❌ — Nischen; Graph-Fälle mit xyflow.

### React Components Sandboxes

- **storybook** 🔁 — bereits im Stack (v10). Ausbauen: Stories als Vertrag für die Token-Schichten, a11y-Addon, visuelle Regression ist über Playwright schon angelegt.
- **react-cosmos** ❌, **bit** ❌ — Storybook ist gesetzt.

### React Forms

- **tanstack-form** 🧩 — Feature-Pick: ökosystem-konsistent, wenn komplexe Formulare zurückkommen (+ zod 4 zur Validierung).
- **react-hook-form** ❌ — war im alten Stack, wurde entfernt; nicht reaktivieren.
- **react-jsonschema-form / formily** ❌ — Enterprise-Schema-Formulare ohne Use-Case.

### React Tables and Grids

- **tanstack-table** ✅ — Shortlist #5.
- **react-grid-layout** 🧩 — nur falls frei anordenbare Widgets geplant; sonst CSS-Grid-Bento. (Für Drag & Drop generell auch dnd-kit ansehen — steht nicht in der Liste.)
- **react-data-grid** ❌ — tanstack-table deckt es ab.

### React Maps

- **react-map-gl / react-leaflet** ❌ — kein Karten-Use-Case.

### React Charts

Im aktuellen Stack ist keine Chart-Bibliothek erkennbar — die Entscheidung fällt erst mit dem ersten echten Chart-Feature:

- **recharts** 🧩 — dann erste Wahl (shadcn-Chart-Rezepte bauen darauf).
- **visx** 🧩 — Escape-Hatch für vollständig eigene kosmische Visualisierungen (z. B. Astro-Rad).
- **xyflow** 🧩 — Feature-Pick Archetypen-Konstellation (Node-Graph, kein klassisches Chart).
- **victory / nivo** ❌ — redundant zur recharts-Empfehlung.
- **react-vis** ❌ — praktisch unmaintained.

### React Renderers

- **react-three-fiber** ❌ vorerst — Three.js wurde aus dem Stack entfernt; nur bei einem 3D-Comeback (Orb) wieder relevant, dann R3F 9 (React-19-kompatibel).
- **react-pdf** 🧩 — Feature-Pick Reading-Export.
- **remotion** 🧩 — Feature-Pick Reel-Pipeline (Lizenz beachten).
- **markdown-to-jsx** ❌ — falls Markdown gerendert wird, bei react-markdown bleiben.
- **ink** ❌, **react-figma** ❌.

### React Internationalization

- 🔁 — i18n (de/en) ist bereits gelöst. **formatjs / react-i18next / react-intlayer** ❌ — kein Grund, die bestehende Lösung zu ersetzen.

### React Graphics and Animations

- **framer-motion** 🔁 — als motion 13 im Stack.
- **auto-animate** ✅ — Shortlist #6.
- **react-parallax-tilt** 🧩 — Feature-Pick Tarot-Tilt.
- **react-spring** ❌ — zweite Animations-Engine vermeiden.
- **react-tsparticles** ❌ — Kosmos-Effekte über eigene Keyframes/Canvas; kostet Runtime.
- **simple-parallax-js** ❌ — trivial selbst gebaut.

### React Integration / Real Apps

- **rescript / fulcro** ❌ — andere Sprach-Ökosysteme.
- **Real Apps** — Referenz: `readest` und `wave` als Beispiele polierter React-Architektur.

### React Native (alle Unterabschnitte)

❌ für jetzt — loum.ai ist Web (Cloudflare Workers). Bei einer nativen App: Einstieg über Expo, Token-Wiederverwendung über Tailwind-kompatible RN-Ansätze; dann neu bewerten.

### Randnotiz: Abschnitte vor „React Styling"

- **zustand** 🔁 — inzwischen im Stack (v5); die v1-Randnotiz hat sich erledigt.
- **tanstack-query** ❌ vorerst — Start-Loader/Server-Functions decken Data-Fetching ab; 🧩 erst, falls client-seitiges Caching über die Loader hinaus wächst (integriert sich dann sauber mit Start).

---

## Empfohlene Reihenfolge (v2)

1. **`@ai-sdk/react` im Client** — die Chat-Lücke schließen; Server spricht das Protokoll schon.
2. **react-error-boundary + react-scan** — je unter einem Tag, sofortiger Nutzen.
3. **RTL-Komponententests** unter bun test — die fehlende Test-Ebene zwischen Unit und E2E.
4. **Prüfpunkte im Monorepo** abarbeiten (eine Lösung pro Kategorie, Renovate/Dependabot).
5. **auto-animate** in Listen-UIs.
6. **tanstack-table** mit der ersten echten Tabelle.
7. **Feature-Picks** (xyflow, tanstack-form + zod, Tilt, PDF, QR, Calendar, remotion) jeweils mit dem zugehörigen Feature.

*Kein Upgrade-Zug mehr nötig: Der v1-Migrationsplan (Tailwind 4, Router, Vite, React 19, motion) ist im echten Repo bereits Realität — die Aufgabe ist jetzt Aktuell-Halten, nicht Aufholen.*
