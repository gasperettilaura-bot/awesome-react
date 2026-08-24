# LOUM Design System — Auswertung der awesome-react-Liste

**Scope:** Alle Abschnitte der [README](README.md) ab **„React Styling"** bis zum Ende (inkl. React Native), bewertet gegen den bestehenden Stack des loum.ai-Frontends.
**Stand:** August 2026

## Ist-Stack loum.ai (Referenz für die Bewertung)

Das loum.ai-Frontend (LOUM Design System v10) basiert auf:

- **Build/Sprache:** Vite 5, React 18, TypeScript
- **Styling:** Tailwind CSS 3.4 + CSS-Variablen-Tokens (HSL), `tailwindcss-animate`, Glass-/Glow-/Aurora-Utilities in `tailwind.config.ts`
- **Komponenten:** shadcn/ui (komplettes Radix-Set, ~50 Komponenten) + eigene LOUM-Komponenten (`glass-card`, `liora-button`, `animated-border-card`, `section-header`, …), `class-variance-authority` + `tailwind-merge`
- **Motion/3D:** framer-motion 12, react-three-fiber 8 + drei (Orb), eigene Keyframes (aurora-drift, breathe, float, pulse-glow)
- **Daten/Forms:** TanStack Query 5, react-hook-form 7 + zod 3, Supabase (Auth, DB, Edge Functions)
- **Sonstiges:** react-router-dom 6, lucide-react, sonner, cmdk, embla-carousel, vaul, react-day-picker, recharts 2, next-themes, react-markdown

Die Bewertung fragt also nicht „ist die Bibliothek gut?", sondern **„bringt sie dem LOUM Design System etwas, das der Stack noch nicht hat — ohne Doppelungen zu erzeugen?"**

**Legende:** ✅ übernehmen · 🔁 schon im Stack (mit Empfehlung) · 🧩 bei Bedarf / Feature-getrieben · ❌ nicht übernehmen

---

## Das Beste — Shortlist zur Übernahme

| # | Bibliothek | Warum für loum.ai |
|---|-----------|-------------------|
| 1 | **storybook** | Die fehlende Werkbank des Design Systems. Das Playbook enthält bereits ein handgebautes statisches `storybook.html` — echtes Storybook (v9, Vite-Builder) ersetzt das: Stories für `glass-card`, `liora-button`, `section-header` & Co., Dokumentation der Tokens, a11y-Addon, später visuelle Regression (Chromatic). |
| 2 | **ai-sdk** (Vercel AI SDK) | Kernprodukt-Hebel: `DeepDiveChat.tsx` parst den SSE-Stream der Edge Function heute von Hand. `useChat`/`streamText` ersetzen das durch getestete Streaming-Primitives inkl. Abbruch, Fehler- und Ladezuständen. Das Lovable-Gateway ist OpenAI-kompatibel → `@ai-sdk/openai-compatible`-Provider (vor Umbau kurz verifizieren); die Edge Functions (Deno, `npm:`-Imports) können das SDK serverseitig nutzen. |
| 3 | **react-scan** | Dev-only Performance-Scanner ohne Code-Änderung. Genau richtig für die animationslastige LOUM-UI (Aurora, Glass-Blur, Orb): macht vermeidbare Re-Renders sichtbar, bevor sie das 60fps-Gefühl kosten. |
| 4 | **react-error-boundary** | Kleine, etablierte Bibliothek. Dashboard-Widgets, Orb (WebGL!) und Chat einzeln absichern, damit ein Fehler nicht die ganze App weiß rendert. Passt zum Widget-/Bento-Konzept. |
| 5 | **react-testing-library** (+ Vitest statt Jest) | Test-Fundament fürs Design System: Komponenten-Tests Vite-nativ mit Vitest + RTL. `@playwright/test` liegt bereits in den Dependencies — dazu echte E2E-Smokes (Login, Tarot-Reading, Chat) einrichten. |
| 6 | **auto-animate** | Zero-Config-Übergänge für Listen (Community-Feed, Verlauf, Widget-Listen), wo framer-motion-Varianten Overkill sind. ~2 kB, eine Zeile pro Container. |
| 7 | **tanstack-table** | Sobald echte Datentabellen kommen (Investor-/Community-/Admin-Ansichten): headless, stylt sich vollständig über die vorhandenen shadcn-Table-Styles — kein Fremd-Look. |

**Feature-Picks** (übernehmen, sobald das jeweilige Feature ansteht):

- **xyflow (React Flow)** 🧩 — Archetypen-„Konstellation" als interaktiver Graph (Knoten = Archetypen/Karten, Kanten = Beziehungen). Sehr gut gepflegt; wäre ein Signature-Feature.
- **react-parallax-tilt** 🧩 — Holo-/Tilt-Effekt beim Hover über Tarot-Karten. Winzig, gepflegt, maximaler Brand-Fit.
- **react-pdf** 🧩 — Readings/Reports als gestaltetes PDF exportieren (Deep-Dive-Zusammenfassung, Archetyp-Dossier).
- **react-big-calendar** 🧩 — falls der Cosmic Calendar eine klassische Monats-/Wochenansicht im Web braucht; für rein „kosmische" Custom-Darstellungen eher selbst bauen.
- **react-i18next** 🧩 — Standard-Wahl, sobald DE/EN ansteht (`.ai`-Domain, internationale Zielgruppe). formatjs ist schwerer, intlayer zu jung.
- **qrcode.react** 🧩 — Sharing-Features (Reading teilen, Profil-QR). Klein und stabil.
- **remotion** 🧩 — kein Design-System-, aber ein Content-Baustein: Daily-Card-Reels programmatisch rendern (Stella's-Safari-Pipeline). Lizenzmodell beachten (ab bestimmter Firmengröße kostenpflichtig).

---

## Konsolidierung: Doppelungen im eigenen Stack zuerst auflösen

Die wichtigste Erkenntnis aus dem Abgleich ist nicht „mehr übernehmen", sondern **pro Kategorie genau eine Lösung**:

1. **Toasts:** `sonner` behalten, die alten shadcn-Dateien `toast.tsx`/`toaster.tsx`/`use-toast.ts` entfernen (shadcn selbst empfiehlt inzwischen Sonner). `react-hot-toast` aus der Liste wird damit nicht gebraucht.
2. **Icons:** nur `lucide-react` (Version ist mit 0.462 veraltet → aktualisieren). Kein Mix mit react-icons/heroicons — ein Icon-Stil ist Teil der Design-System-Identität.
3. **Carousel:** `embla-carousel` behalten (steckt im shadcn-Carousel). Swiper/keen-slider nicht zusätzlich.
4. **Animation:** framer-motion als einzige Physik-Engine (heißt heute `motion`, Import `motion/react`). Kein react-spring daneben; auto-animate nur als ergänzendes Utility für simple Fälle.
5. **Command-Palette:** `cmdk` ist schon da (shadcn `command.tsx`) → ⌘K-Palette damit bauen, kein kbar.
6. **Datepicker:** `react-day-picker` (shadcn `calendar.tsx`) behalten, kein react-datepicker.

## Upgrade-Pfad (aus der Liste bestätigt, je eigener PR)

| Paket | Von → Nach | Hinweis |
|-------|-----------|---------|
| tailwindcss | 3.4 → **4.x** | CSS-first-Konfiguration (`@theme`) — die LOUM-Tokens wandern von `tailwind.config.ts` in CSS; `tailwindcss-animate` → `tw-animate-css`. Größter Einzelschritt, lohnt als dediziertes Projekt. |
| vite | 5 → **7** | Schnellere Builds; vorher Plugin-Kompatibilität prüfen. |
| react-router-dom | 6 → **7** | Von den Maintainern als sanfte Migration ausgelegt (Future-Flags in v6 aktivieren, dann Paketwechsel). |
| framer-motion | 12 → **motion** | Reines Rename (`motion/react`), API bleibt. |
| recharts | 2 → **3** | shadcn-`chart.tsx`-Wrapper mittesten. |
| zod | 3 → **4** | Zusammen mit `@hookform/resolvers` aktualisieren. |
| react / R3F | 18 → 19, dann R3F 9 + drei 10 | R3F 9 setzt React 19 voraus — als letzten Schritt planen, Orb danach visuell abnehmen. |

---

## Bewertung Abschnitt für Abschnitt

### React Styling

- **styled-components** ❌ — offiziell im **Maintenance-Modus** (seit 2025, keine neuen Features). Runtime-CSS-in-JS widerspricht dem Tailwind-Ansatz. Klare Absage.
- **emotion** ❌ — gepflegt, aber ein zweites Styling-Paradigma neben Tailwind erzeugt nur Inkonsistenz.
- **vanilla-extract** ❌ — technisch elegant (zero-runtime), aber die Tokens liegen bereits als CSS-Variablen + Tailwind-Theme vor. Kein Mehrwert, doppelte Wahrheit.

> **Fazit Styling:** Nichts übernehmen — der bestehende Ansatz (Tailwind + CSS-Variablen + CVA) ist genau der aktuelle Stand der Technik. Die richtige Investition ist das **Tailwind-4-Upgrade**, nicht eine zusätzliche Bibliothek.

### React Icon Libraries

- **lucide-react** 🔁 — gesetzt; nur Version aktualisieren.
- **react-icons** ❌ (Stil-Mix, große Installation), **heroicons** ❌ (redundant zu Lucide), **thesvg** ❌ (Nische; für Brand-Logos bei Bedarf gezielt lösen).

### React Routing

- **react-router** 🔁 — bleibt; Upgrade auf v7 einplanen (s. o.).
- **tanstack-router** ❌ — exzellente Typsicherheit, aber eine Migration bringt dem Design System nichts; nur bei einem Neubau erwägen.
- **speedy-router** ❌ — zu jung/experimentell für Produktions-Wetten.

### React Development Tools

- **vite** 🔁 — gesetzt; Upgrade auf 7.
- **react-scan** ✅ — Top-Pick #3 (s. Shortlist).
- **why-did-you-render** ❌ — gleicher Zweck wie react-scan, aber invasiver (Monkey-Patching); react-scan reicht.
- **eslint-plugin-react** 🧩 — ESLint 9 + hooks/refresh-Plugins sind schon da; bei Gelegenheit ergänzen, kein Muss.
- **parcel** ❌ (Vite ist gesetzt), **reactotron** ❌ (React-Native-fokussiert).

### React Libraries

- **ai-sdk** ✅ — Top-Pick #2 (s. Shortlist).
- **react-error-boundary** ✅ — Top-Pick #4.
- **floating-ui** ❌ direkt — steckt bereits in Radix unter der Haube; keine Direktnutzung nötig.
- **downshift** ❌ — Radix Select + cmdk decken Combobox-Fälle ab.
- **loadable-components** ❌ — `React.lazy` + Suspense reichen unter Vite.
- **react-uploady** ❌ — Uploads laufen besser direkt über Supabase Storage; eine Upload-Framework-Schicht lohnt erst bei komplexen Upload-UIs.
- **preact** ❌ — nicht relevant.

### React Testing

- **react-testing-library** ✅ — mit **Vitest** statt Jest (Vite-nativ, gleiche API).
- **playwright** 🔁 — liegt schon in den Dependencies, wird aber nicht sichtbar genutzt → E2E-Smokes für die kritischen Flows einrichten.
- **jest** ❌ (Vitest ist das Jest-Äquivalent für Vite), **cypress** ❌ (Playwright ist gesetzt).

### React Awesome Components

- **react-hot-toast** ❌ — sonner ist gesetzt (s. Konsolidierung).
- **kbar** ❌ — cmdk ist gesetzt.
- **swiper / keen-slider** ❌ — embla ist gesetzt.
- **react-select** ❌ — Fremd-Look, schwer auf LOUM-Tokens zu trimmen; Radix/cmdk decken es ab.
- **react-datepicker** ❌ — react-day-picker ist gesetzt.
- **react-qrcode (qrcode.react)** 🧩 — für Sharing-Features (s. Feature-Picks).
- **react-insta-stories** 🧩/❌ — die Idee (Daily-Card als Story-Format für eine Instagram-sozialisierte Zielgruppe) ist gut, die Bibliothek nur mäßig gepflegt → das Story-Pattern lieber mit embla + motion selbst bauen.
- **react-big-calendar** 🧩 — s. Feature-Picks (Cosmic Calendar).
- **puck** ❌ — visueller Page-Builder, kein Use-Case.
- **react-archer / react-complex-tree / tagify / heart-switch / json-edit-react** ❌ — Nischen ohne aktuellen Bedarf (Graph-Fälle besser mit xyflow).

### React Components Sandboxes

- **storybook** ✅ — Top-Pick #1 (s. Shortlist).
- **react-cosmos** ❌ — leichter, aber kleineres Ökosystem; Storybook ist mit Playbook-Feedback-Prozess und Addons die bessere Wahl.
- **bit** ❌ — Komponenten-Marktplatz/Monorepo-Tooling, Overkill für ein Ein-App-Design-System.

### React Forms

- **react-hook-form** 🔁 — gesetzt (inkl. zod-Resolver); zod-4-Upgrade einplanen.
- **tanstack-form** ❌ — kein Migrationsnutzen gegenüber RHF.
- **react-jsonschema-form / formily** ❌ — Schema-getriebene Enterprise-Formulare, kein Use-Case.

### React Tables and Grids

- **tanstack-table** ✅ — Top-Pick #7 (bei Bedarf einbauen).
- **react-grid-layout** 🧩 — nur falls Nutzer:innen ihre Dashboard-Widgets frei anordnen sollen; bis dahin reicht das CSS-Grid-Bento-Layout. (Für Drag & Drop generell wäre auch dnd-kit einen Blick wert — steht nicht in der Liste.)
- **react-data-grid** ❌ — tanstack-table deckt es headless und token-konform ab.

### React Maps

- **react-map-gl / react-leaflet** ❌ — kein Karten-Use-Case bei loum.ai.

### React Charts

- **recharts** 🔁 — gesetzt (shadcn-Charts basieren darauf); Upgrade auf v3.
- **xyflow** 🧩 — Feature-Pick Archetypen-Konstellation (s. o.).
- **visx** 🧩 — Escape-Hatch für vollständig eigene kosmische Visualisierungen (z. B. Astro-Rad), wenn recharts an Grenzen stößt. Erst dann.
- **victory / nivo** ❌ — redundant zu recharts.
- **react-vis** ❌ — praktisch unmaintained (Uber-Projekt, seit Jahren ohne Releases).

### React Renderers

- **react-three-fiber** 🔁 — gesetzt (Orb); v9 erst mit React 19.
- **react-pdf** 🧩 — Feature-Pick Reading-Export.
- **remotion** 🧩 — Feature-Pick Content-Pipeline (Lizenz beachten).
- **markdown-to-jsx** ❌ — react-markdown ist gesetzt; Wechsel brächte nur marginale Bundle-Ersparnis.
- **ink** ❌ (CLI-Renderer), **react-figma** ❌ (unreif; Figma-Sync läuft besser über die bestehende Figma-MCP-Anbindung).

### React Internationalization

- **react-i18next** 🧩 — Standard-Empfehlung, sobald Mehrsprachigkeit ansteht (s. Feature-Picks).
- **formatjs** ❌ — mächtig, aber schwergewichtiger Workflow (Message-Extraktion) für die Teamgröße.
- **react-intlayer** ❌ — interessant, aber zu junges Ökosystem für eine Kerninfrastruktur-Wette.

### React Graphics and Animations

- **framer-motion** 🔁 — gesetzt; Rename auf `motion` beim nächsten Major mitnehmen.
- **auto-animate** ✅ — Top-Pick #6.
- **react-parallax-tilt** 🧩 — Feature-Pick Tarot-Karten-Tilt (s. o.).
- **react-spring** ❌ — zweite Animations-Engine vermeiden.
- **react-tsparticles** ❌ — Partikel-/Kosmos-Effekte sind mit eigenen Keyframes + R3F bereits abgedeckt; tsparticles kostet Runtime und wird nur noch schleppend gepflegt.
- **simple-parallax-js** ❌ — bei Bedarf ist Parallax mit CSS/motion trivial selbst gebaut.

### React Integration / Real Apps

- **rescript / fulcro** ❌ — andere Sprach-Ökosysteme, nicht relevant.
- **React Real Apps** — keine Bibliotheken, aber als Referenz nützlich: `readest` und `wave` sind gute Beispiele für polierte React-UI-Architektur in Produktion.

### React Native (alle Unterabschnitte)

❌ für jetzt — loum.ai ist eine Web-App. Sollte eine native App kommen, ist der Einstieg **Expo** (steht in der Liste) plus react-navigation; die Design-Tokens ließen sich über Tailwind-kompatible RN-Ansätze (z. B. NativeWind, nicht in der Liste) wiederverwenden. Erst bei konkreter Mobile-Entscheidung neu bewerten.

### Randnotiz: Abschnitte vor „React Styling"

Außerhalb des Scopes, aber ein Befund aus dem Code-Abgleich: loum.ai hat **kein Client-State-Management** (nur TanStack Query + lokale States). Das ist aktuell in Ordnung; wächst der globale UI-State (Player, Onboarding-Flows, Cross-Widget-State), ist **zustand** aus dem State-Management-Abschnitt die passende leichte Ergänzung.

---

## Empfohlene Reihenfolge

1. **Konsolidierung** (Toast auf sonner vereinheitlichen, Lucide aktualisieren) — klein, sofort.
2. **react-error-boundary + react-scan** — je < 1 Tag, sofortiger Nutzen.
3. **Storybook aufsetzen** und die LOUM-Kernkomponenten dokumentieren — macht das Design System erstmals „begehbar".
4. **Vitest + RTL + Playwright-Smokes** — Fundament vor den Upgrades.
5. **AI SDK in DeepDiveChat** — größter Produkt-Hebel, nach Gateway-Verifikation.
6. **Upgrade-Zug** (Tailwind 4 → Router 7 → Vite 7 → recharts 3 → zod 4 → React 19/R3F 9), je eigener PR auf dem frischen Test-Fundament.
7. **Feature-Picks** (xyflow, Tilt, PDF, i18n, …) jeweils mit dem zugehörigen Feature.
