# Marta CRM — AI-powered relationship command center

> AI-aware kontekst projektu. Czytane automatycznie przez Claude Code (i innych asystentów AI). Trzymaj zwięźle — szczegóły są w `CONTEXT.md`, `DATA_MODEL.md`, `WORKSTREAMS.md`, `DEMO.md`.

## Co to jest

CRM dla **business opportunities** (investors, design partners, HR candidates) który **sam się aktualizuje** na podstawie wrzucanych notatek, PDFów i transkryptów. Single-user MVP dla persony **Marty** — founderki w trakcie seed roundu.

## Przeczytaj najpierw

| Plik | Zawartość |
|---|---|
| `CONTEXT.md` | Persona, problem, scope (in / out), success criteria |
| `DATA_MODEL.md` | DB schema, entities, shared types (**DRAFT — finalizujemy dzień 0**) |
| `WORKSTREAMS.md` | 12 paczek pracy do równoległego podziału |
| `DEMO.md` | Demo scenario (5-min walkthrough) |
| `dataset-marta.csv` | Seed data: 50 osób z relacjami Marty |

## Zasady projektu

- **Single-user MVP** — brak autha na demo, brak teams, brak multi-tenant
- **Input = paste tekstu** (textarea). Bez integracji, bez audio. Integracje to osobny stretch task.
- **Stack per-feature** — owner paczki wybiera własny stack. **SHARED między wszystkimi**: data model (`DATA_MODEL.md`) i API contracts.
- **Conventional commits, bez scope'a** (zgodnie z globalnym CLAUDE.md autora)

## Czego NIE budujemy w MVP

- Kanban view (stage'y widoczne na Person profile + filtry list — kanban wycięty świadomie)
- Multi-user / teams / auth
- Mobile apps
- Integracje Google / Slack / Atlassian / Notion (stretch task — `S1` w WORKSTREAMS.md)
- Audio transcription / Whisper (stretch task — `S2`)
- Native push notifications (tylko email przez Resend)

## Hackathon — constraints

- **2 tygodnie**, **5 osób** pracujących równolegle
- **Deliverable**: hosted prototyp na publicznym URL + live demo dla jurorów
- **Stack**: per-feature owner decyduje (patrz `WORKSTREAMS.md`)
- **Demo data**: seed z `dataset-marta.csv` (50 osób)

## Wytyczne dla AI asystentów

- Nie dodawaj feature'ów spoza scope'u z `CONTEXT.md` bez zgody
- Przy zmianach data model — aktualizuj `DATA_MODEL.md` i flaguj cross-team
- Stage'y są **dynamiczne** (AI może zaproponować nowy stage / branch) — nie hardcode'uj enum
- Commitments to **first-class entity** (osobna tabela), nie pole w person.notes
- Przy AI extraction zawsze zwracaj **confidence + source** — Marta musi mieć możliwość Accept/Reject
- Wszystkie AI-generated zmiany przechodzą przez **review queue** Marty zanim trafią do DB (nie auto-apply)
