# Data Model — DRAFT

> 🚧 **Status: DRAFT**. Finalizujemy na **Dzień 0** wszyscy razem. Po zatwierdzeniu zmiany wymagają broadcastu cross-team.

Ten dokument jest **shared contract** — wszystkie paczki (F2, A, B, C, D, E, F, G1) z niego korzystają. Update == ping team.

## Entities — high level

```
pipeline (1) ──< stage (N) ──< person (N)
                                  │
                                  ├──< interaction (N)
                                  ├──< commitment (N)
                                  └──< stage_change (N)    ← audit log

input (1) ──< extraction (N) ──► (person | commitment | stage_change)
```

## Tabele

### `pipeline`

| Field | Type | Notes |
|---|---|---|
| `id` | uuid (pk) | |
| `category` | enum: `'investor' \| 'design_partner' \| 'hire'` | jeden pipeline per kategoria w MVP |
| `name` | text | np. "Seed Round 2026" |
| `created_at` | timestamptz | |

**Konwencja MVP:** jeden pipeline per kategoria. W przyszłości Marta może mieć więcej (Series A pipeline obok seed).

### `stage`

| Field | Type | Notes |
|---|---|---|
| `id` | uuid (pk) | |
| `pipeline_id` | uuid (fk) | |
| `name` | text | np. "Warm Intro Pending", "First Meeting", "Diligence" |
| `order` | int | sortowanie w listach / filtrach |
| `is_terminal` | boolean | true dla "Passed" / "Committed" / "Rejected" — stage'y po których nie ma już ruchu |
| `created_at` | timestamptz | |
| `created_by` | enum: `'system' \| 'ai' \| 'user'` | AI może proponować nowe stage'y |

**Krytyczne:** stage'y są **dynamiczne**, NIE hardcoded enum. AI w extraction może zaproponować nowy stage; trafia do review queue Marty.

### `person`

| Field | Type | Notes |
|---|---|---|
| `id` | uuid (pk) | |
| `name` | text | |
| `category` | enum: `'investor' \| 'design_partner' \| 'hire'` | dla szybkiego filtra (denormalized z pipeline) |
| `current_stage_id` | uuid (fk, nullable) | nullable dla nowo dodanych |
| `source` | text | "introduced by Paweł", "met at Infoshare" — z CSV `source` |
| `relationship` | text | krótki opis relacji — z CSV `relationship` |
| `notes` | text | free text, edytowalny |
| `tags` | text[] | np. `['lead', 'active', 'fundraising']` |
| `last_interaction_at` | timestamptz (nullable) | denormalized — najnowsza `interaction.happened_at` |
| `created_at` | timestamptz | |
| `updated_at` | timestamptz | |

### `interaction`

Wpis w timeline — coś się wydarzyło z personą.

| Field | Type | Notes |
|---|---|---|
| `id` | uuid (pk) | |
| `person_id` | uuid (fk) | |
| `type` | enum: `'meeting' \| 'email' \| 'call' \| 'slack' \| 'message' \| 'note' \| 'other'` | |
| `summary` | text | krótki podsumowanie (1–3 zdania) |
| `happened_at` | timestamptz | |
| `source_input_id` | uuid (fk, nullable) | link do oryginalnego paste skąd to wyekstrahowano |
| `created_by` | enum: `'ai' \| 'user'` | |
| `created_at` | timestamptz | |

### `commitment` — **first-class entity, killer feature**

| Field | Type | Notes |
|---|---|---|
| `id` | uuid (pk) | |
| `person_id` | uuid (fk) | |
| `direction` | enum: `'i_owe' \| 'they_owe'` | **dwukierunkowe!** Marta winna im LUB oni winni Marcie |
| `description` | text | "Send cohort analysis", "Send SAFE docs", "Intro to Beata" |
| `due_date` | date (nullable) | ekstrahowane przez AI z notatki lub manual |
| `status` | enum: `'open' \| 'done' \| 'overdue' \| 'dropped' \| 'snoozed'` | `overdue` jest computed (open + due < today), albo materializowany cronem |
| `source_input_id` | uuid (fk, nullable) | link do paste skąd to wyekstrahowano |
| `confidence` | numeric(3,2) | 0.00–1.00, tylko dla AI-extracted (null dla manual) |
| `accepted_by_user` | boolean | dla AI-extracted: czy Marta zaakceptowała? Domyślnie `false` dopóki nie review |
| `created_by` | enum: `'ai' \| 'user'` | |
| `created_at` | timestamptz | |
| `updated_at` | timestamptz | |
| `completed_at` | timestamptz (nullable) | |

### `stage_change` — audit log

Każda zmiana stage'a personu — żeby było widać history na profilu.

| Field | Type | Notes |
|---|---|---|
| `id` | uuid (pk) | |
| `person_id` | uuid (fk) | |
| `from_stage_id` | uuid (fk, nullable) | null dla pierwszego ustawienia |
| `to_stage_id` | uuid (fk) | |
| `changed_at` | timestamptz | |
| `source` | enum: `'ai' \| 'user'` | |
| `source_input_id` | uuid (fk, nullable) | |
| `confidence` | numeric(3,2) (nullable) | tylko dla AI |
| `note` | text (nullable) | dlaczego ta zmiana — np. "after IC scheduled" |

### `input` — raw paste

| Field | Type | Notes |
|---|---|---|
| `id` | uuid (pk) | |
| `content` | text | raw text wklejony przez Martę |
| `source_type` | enum: `'paste' \| 'pdf' \| 'meeting_transcript' \| 'email'` | MVP: tylko `paste` |
| `status` | enum: `'pending' \| 'processing' \| 'processed' \| 'failed'` | |
| `processed_at` | timestamptz (nullable) | |
| `created_at` | timestamptz | |

### `extraction` — co AI znalazło w `input`

Każdy paste produkuje N extractions (potencjalnie różnych typów). Wszystkie idą do **review queue** Marty.

| Field | Type | Notes |
|---|---|---|
| `id` | uuid (pk) | |
| `input_id` | uuid (fk) | |
| `entity_type` | enum: `'person' \| 'commitment' \| 'interaction' \| 'stage_change'` | |
| `entity_id` | uuid (fk, nullable) | populated PO akceptacji przez Martę |
| `payload` | jsonb | proponowane dane (np. `{name, category, suggested_stage}` dla person) |
| `confidence` | numeric(3,2) | |
| `source_span` | text (nullable) | fragment oryginalnego tekstu skąd to wyciągnięto — do "show me where" |
| `status` | enum: `'pending' \| 'accepted' \| 'rejected' \| 'auto_applied'` | `auto_applied` dla wysokiego confidence > 0.9 (opcjonalnie) |
| `created_at` | timestamptz | |
| `decided_at` | timestamptz (nullable) | |

## Zasady projektowe

1. **AI nie pisze bezpośrednio do `person`/`commitment`/`stage`** — pisze do `extraction` (review queue). Marta akceptuje, wtedy entity powstaje.
   - Wyjątek (opcjonalny): auto-apply przy confidence > 0.9, ale wciąż widoczne w UI jako "AI did this" z możliwością undo.

2. **Stage'y są dynamiczne** — `stage` to osobna tabela, NIE enum. AI może zaproponować nowy stage (też przez `extraction.payload = {action: 'add_stage', name: '...'}`).

3. **Commitment.direction jest niezbywalny** — "i_owe" vs "they_owe" to fundamentalnie różne UX. Today view ma 2 sekcje.

4. **`source_input_id` to złoty link** — każde AI-generated entity musi mieć źródłowy `input`. Marta może zawsze kliknąć "skąd to wziąłeś?" → highlight w oryginale.

5. **`last_interaction_at` jest denormalized** — bo Today view sortuje masowo po tym, nie chcemy JOIN za każdym razem.

## TypeScript types — skeleton

```typescript
type Category = 'investor' | 'design_partner' | 'hire'

type Pipeline = {
  id: string
  category: Category
  name: string
  createdAt: Date
}

type Stage = {
  id: string
  pipelineId: string
  name: string
  order: number
  isTerminal: boolean
  createdBy: 'system' | 'ai' | 'user'
  createdAt: Date
}

type Person = {
  id: string
  name: string
  category: Category
  currentStageId: string | null
  source: string
  relationship: string
  notes: string
  tags: string[]
  lastInteractionAt: Date | null
  createdAt: Date
  updatedAt: Date
}

type Commitment = {
  id: string
  personId: string
  direction: 'i_owe' | 'they_owe'
  description: string
  dueDate: Date | null
  status: 'open' | 'done' | 'overdue' | 'dropped' | 'snoozed'
  sourceInputId: string | null
  confidence: number | null
  acceptedByUser: boolean
  createdBy: 'ai' | 'user'
  createdAt: Date
  updatedAt: Date
  completedAt: Date | null
}

type Interaction = {
  id: string
  personId: string
  type: 'meeting' | 'email' | 'call' | 'slack' | 'message' | 'note' | 'other'
  summary: string
  happenedAt: Date
  sourceInputId: string | null
  createdBy: 'ai' | 'user'
}

type Extraction = {
  id: string
  inputId: string
  entityType: 'person' | 'commitment' | 'interaction' | 'stage_change'
  entityId: string | null
  payload: Record<string, unknown>
  confidence: number
  sourceSpan: string | null
  status: 'pending' | 'accepted' | 'rejected' | 'auto_applied'
}
```

## Open questions (do rozstrzygnięcia na Dzień 0)

- [ ] Czy `extraction.status = 'auto_applied'` ma sens, czy zawsze przez Martę? **Rekomendacja:** auto-apply przy confidence > 0.9, ale flagowane w UI.
- [ ] Czy potrzebujemy `tags` osobnej tabeli, czy `text[]` wystarczy? **Rekomendacja:** `text[]` w MVP (filtrowanie GIN index).
- [ ] Czy `interaction.summary` jest AI-generated, czy raw fragment? **Rekomendacja:** AI-generated short summary (1–3 zdania), z linkiem do `source_input_id` dla pełnego kontekstu.
- [ ] Czy historia `person.notes` (edits) ma być versionowana? **Rekomendacja:** NIE w MVP. Last-write-wins.
