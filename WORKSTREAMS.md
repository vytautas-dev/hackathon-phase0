# Workstreams — 12 paczek pracy

> Każda paczka = self-contained, można podjąć niezależnie. Stack per-paczka. Owner sam wybiera.

## Zasady

- **Foundation paczki (F1–F3) MUSZĄ być zamknięte przed startem Parallel paczek** — bo zawierają shared data model i API contracts od których wszystko zależy
- **SHARED między wszystkimi**: data model (`DATA_MODEL.md`) + API contract (`API_CONTRACT.md`)
- Zmiana data model = update `DATA_MODEL.md` + ping cross-team (broadcast w Slack/Discord)
- Każda paczka kończy się **demo-able output** (nawet jeśli z mockami)

---

## Foundation (dzień 0–1)

### F1 — Data Model + Shared Types
- **Co:** DB schema (person, interaction, commitment, stage, pipeline, input, extraction), TS types / Zod schemas, sample data
- **Dep:** —
- **Estimated:** 1 dzień
- **Deliverable:** `DATA_MODEL.md` finalized + schema migrations + types package (lub plik z typami)
- **Blokuje:** F2, A, B, C, D, E, F, G1, G2

### F2 — API Contract
- **Co:** Lista endpointów + payload shapes, mock responses (żeby UI mogło ruszyć bez backendu)
- **Dep:** F1
- **Estimated:** 0.5 dnia
- **Deliverable:** `API_CONTRACT.md` (OpenAPI lub lista endpointów z przykładami)

### F3 — Demo Scenario Script
- **Co:** Golden path: co wklejamy → co AI zwraca → jakie ekrany pokazujemy → jak kończymy
- **Dep:** —
- **Estimated:** 0.5 dnia
- **Deliverable:** `DEMO.md` finalized + `demo-data/` (pre-prepared paste samples)

---

## Parallel features (tydzień 1–2, po F1+F2)

### A — AI Extraction
- **Co:** Prompt + tool schema (Anthropic tool use albo OpenAI structured output): paste tekstu → JSON z osobami / stage updates / commitments. Confidence per extraction.
- **Dep:** F1
- **Estimated:** 3–4 dni
- **Stack:** per owner. Sugerowany: Claude Sonnet 4.6 z tool use, server route w wybranym backendzie
- **Deliverable:** endpoint `POST /extract` przyjmujący tekst, zwracający JSON z `extractions[]` (każdy z `entity_type`, `confidence`, `source_span`)

### B — AI Next-step Suggestions
- **Co:** Prompt generujący sugestie ("nie ruszał się 10 dni — zaproponuj follow-up?"). Action button generator: `mailto:` z draftem, plik `.ics`, copy-to-clipboard text.
- **Dep:** F1, A
- **Estimated:** 2–3 dni
- **Deliverable:** endpoint `POST /suggestions/:personId` zwracający `[{ text, action: { type, payload } }]`

### C — Today / Inbox view
- **Co:** UI: dzisiaj + zaległe commitments, grupowanie po pilności, sortowanie. Cross-category (investors + design partners + hires razem). Empty state. Done/Snooze actions.
- **Dep:** F1
- **Estimated:** 2–3 dni
- **Deliverable:** ekran `/` (landing) + components

### D — Person Profile
- **Co:** UI: header (name, category, current stage), timeline interakcji, lista commitments (open / closed), stage history, notes. Edit inline.
- **Dep:** F1
- **Estimated:** 2–3 dni
- **Deliverable:** ekran `/person/[id]` + components

### E — Person List + Manual Edit
- **Co:** 3 listy (filtrowalne) per kategoria. Search po name/notes. CRUD forms (add new person, edit, delete). Stage selector (z możliwością "add new stage").
- **Dep:** F1
- **Estimated:** 2–3 dni
- **Deliverable:** ekrany `/people/investors`, `/people/design-partners`, `/people/hires` + modal forms

### F — Notifications
- **Co:** Email przez Resend. **Daily digest 9am Warsaw time**: zaległe commitments, due dziś, AI-suggested actions. **Overdue alerts**: gdy commitment przekracza due_date. Cron / scheduled job.
- **Dep:** F1
- **Estimated:** 2 dni
- **Deliverable:** scheduled function + email templates (`emails/daily-digest.tsx`, `emails/overdue-alert.tsx`)

---

## Glue (równolegle, ale wymaga koordynacji)

### G1 — CSV Seed Import
- **Co:** Parser `dataset-marta.csv` → wypełnienie DB. **Tylko osoby z tagami** `investor`, `design-partner`, `hire`, `hiring`, `pilot`, `fundraising` etc. (mapowanie tag → category w skrypcie). Ekstrakcja commitments z pola `notes` (heurystyki + AI optional).
- **Dep:** F1
- **Estimated:** 0.5–1 dzień
- **Deliverable:** skrypt `pnpm seed` / `npm run seed` ładujący DB z ~20 osób w 3 kategoriach

### G2 — Deploy + Infra
- **Co:** Hosting (Vercel / Railway / Fly), DB w cloudzie (Supabase / Neon / Railway PG), env vars management, secrets, domena (.vercel.app wystarczy)
- **Dep:** F1 + co najmniej jedno UI (do smoke testu)
- **Estimated:** 1 dzień rozłożone w czasie (incrementally)
- **Deliverable:** publiczny URL, env documentation w README, deploy działa na push do main

### G3 — Design Polish + Smoke Testing
- **Co:** Final UX pass, tooltips, empty states, loading states, error boundaries. E2E walkthrough wg `DEMO.md`. Bug bash.
- **Dep:** wszystkie inne paczki done
- **Estimated:** 1–2 dni (ostatnie 2–3 dni hackathonu)
- **Deliverable:** zielony pass wszystkich kroków z `DEMO.md`

---

## Stretch (poza MVP, jeśli zostaje czas)

### S1 — Integracje
- Slack (webhook → input), Google Workspace (Meet transcript pull, Gmail), Atlassian (Jira tickets jako commitments), Notion (pages as inputs)
- **Pick one** — nie wszystkie

### S2 — Audio input
- Upload audio file → Whisper API → text → existing extraction pipeline

### S3 — Search / Q&A chatbot
- Vector index po interactions + notes. Natural language queries. Sexy ale ryzyko hallucination na demo.

### S4 — Kanban view
- Drag-drop między stage'ami per kategoria. Świadomie wycięty z MVP, ale ładny add-on.

---

## Critical path

```
F1 ──┬──► F2 ──► A ──► B ──┐
     │                       │
     ├──► C ─────────────────┤
     ├──► D ─────────────────┼──► G3 (demo)
     ├──► E ─────────────────┤
     ├──► F ─────────────────┤
     ├──► G1 ────────────────┤
     └──► G2 ────────────────┘
```

**Bottleneck #1:** F1 (data model). Blokuje 9 paczek. **Musi być done na koniec dnia 1.**

**Bottleneck #2:** A (AI extraction). Sam jest największą paczką (3–4 dni) + blokuje B (suggestions). Zacznij równolegle z F1 spike'iem promptów.

**Bottleneck #3:** G3 (smoke + polish). Wymaga że wszystko inne działa. Rezerwuj **ostatnie 2–3 dni** na to. **Nie dodawajcie nowych feature'ów w tym czasie.**

## Tracking

Sugerowane: GitHub Issues z labelkami `F1`, `F2`, ..., `S4`. Każda paczka = jeden epic / issue. PRs taggujesz numerem paczki w tytule.

## "Co jeśli się ślizgamy"

Priorytet **ratunkowy** (jeśli zostają 3 dni i nie wszystko gotowe):
1. **A + C + D + G1 + G2** to absolute minimum demo (extract → today view → person profile, na seed data, hostowane)
2. **B (suggestions), F (email), E (manual edit)** to nice-but-can-cut
3. **G3 polish** zawsze ostatnie 1 dzień
