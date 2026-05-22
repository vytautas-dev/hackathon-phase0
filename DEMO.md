# Demo Scenario — 5 min walkthrough

> Golden path dla live demo na hackathonie. Każdy "beat" ma cel. Trzymaj się tempa — żadnych improwizacji na żywo.

## Setup (przed demo)

- Apka załadowana na publicznym URL (Vercel/Railway, zgodnie z `WORKSTREAMS.md` paczka G2)
- DB seeded z `dataset-marta.csv` (paczka G1) — **tylko osoby z kategorii** `investor`, `design_partner`, `hire` (~20 osób)
- Browser w pełnym ekranie, zoom 110% dla czytelności na projektorze
- Otwarte 2 zakładki: główna z apką + `demo-data/sample-paste.md` z pre-pisanym tekstem do wklejenia
- Drugi monitor / telefon pod ręką jako backup w razie awarii
- AI API klucz: sprawdzony 30 min przed demo (call do `/extract` z curl)

---

## Beat 1 — Problem (45s)

**Mówisz:**
> "To jest Marta. Founderka, w trakcie seed roundu. Ma 12 inwestorów w pipeline, 5 design partnerów, 3 kandydatów do pracy. Co robi w piątek wieczorem? Skroluje notatki i panikuje, bo nie pamięta komu co obiecała."

**Pokazujesz:** fragment `dataset-marta.csv` w split-screen — wyróżnione:
- *"I told her I'd send the one-pager on Monday. That was **three Mondays ago**"* (Aneta)
- *"URGENT: cohort analysis by EOD Thursday. **Don't drop this**"* (Anna)

**Cel beat'a:** w 45 sekund jurorzy rozumieją, że to nie jest "another CRM". To jest *fundraising-grade* pain.

---

## Beat 2 — Today view (45s)

**Robisz:**
- Otwierasz apkę → ląduje na `/` = Today
- Widać 3 sekcje:
  - **🔴 Overdue (5)** — Aneta one-pager, Michał SAFE docs, James Chen reply z sierpnia, ...
  - **⚠️ Today (3)** — Anna cohort analysis (PILNE), Marek FinBridge proposal, ...
  - **👀 Waiting on others (4)** — Paweł→Beata intro, Klaus→Tobias intro, ...

**Mówisz:**
> "To pierwszy ekran rano. Pięć rzeczy spóźnionych, trzy na dziś, cztery rzeczy w których ktoś czeka aż druga strona coś zrobi. Wszystko w jednym miejscu — niezależnie czy to inwestor, design partner czy kandydat do pracy."

**Cel beat'a:** killer feature #1 — Marta widzi WSZYSTKO co ją goni, cross-category.

---

## Beat 3 — Person profile (45s)

**Robisz:**
- Klikasz "Anna Kwiatkowska — Send cohort analysis" → ląduje na `/person/[anna-id]`
- Pokazujesz:
  - Header: "Anna Kwiatkowska · Investor · Stage: **Diligence** · Last contact: 2 weeks ago"
  - Timeline interactions: video call 12.04, IC scheduled Monday, ...
  - Commitments: 2 open (`i_owe`), 1 done
  - Stage history: `Warm Intro → First Meeting → Diligence`
  - **AI suggestion box**: *"Anna nie odpisała 3 dni. Chcesz wysłać follow-up?"* + button **"Draft email"**

**Klikasz "Draft email"** → otwiera się `mailto:` z gotowym draftem:
> "Hi Anna, attaching the cohort analysis ahead of Monday's IC. Also broke out churn by segment as requested..."

**Mówisz:**
> "Cały kontekst osoby w jednym miejscu. AI proaktywnie podpowiada następny krok. Jeden klik — gotowy draft maila."

**Cel beat'a:** wartość per-osoba + AI suggestions w akcji.

---

## Beat 4 — HERO: AI extraction (90s)

**To jest najważniejszy beat. Najwięcej czasu, największy impact.**

**Robisz:**
- Wracasz na Today / klikasz "+ Add input"
- Modal: textarea
- Wklejasz przygotowany tekst (np.):

```
Spotkanie z Anną K., 30 min.

Anna chce Q1 cohort analysis na czwartek EOD. Pyta też o churn rate
per segment — powiedziałam że ogarnę. Wstępnie ma być IC w poniedziałek.

Spotkałam też Marka z FinBridge — pilot ruszamy 1 maja. Potrzebują
proposal i security questionnaire do końca tygodnia. Skontaktuję CC
do ich DPO (dopytam Marka o nazwisko).

Filipa Wójcika decydujemy do piątku — Jakub go chce, ale 28k PLN
net to top of range. Decyzja po kawie w czwartek.
```

- Klikasz "Process with AI" → animacja loading 3–5s
- Wyniki w **review queue**:
  - ✅ **2 commitments** (i_owe): "Send cohort analysis to Anna (due Thu)", "Break out churn by segment for Anna"
  - ✅ **1 stage change**: Anna `Diligence → IC scheduled`
  - ✅ **2 commitments** (i_owe): "Send pilot proposal to Marek", "Send security questionnaire to Marek"
  - ✅ **1 interaction added**: Marek FinBridge meeting note
  - ⚠️ **1 commitment** (i_owe, low confidence): "Decide on Filip by Friday" — Marta widzi confidence 0.65

**Klikasz "Accept all"** (lub demonstrujesz "Reject" na low-confidence)

**Mówisz:**
> "Jeden paste, jeden klik. AI wyciągnął **5 commitments**, **zmienił stage Annie**, **dodał notatkę o Marku**. Marta tylko zatwierdza. To jest serce produktu."

**Cel beat'a:** udowodnienie że to nie jest fake demo. AI faktycznie czyta surowy tekst i strukturyzuje.

---

## Beat 5 — Manual edit (30s)

**Robisz:**
- Klikasz "+ New person"
- Dodajesz "Test Inwestor · Investor · Stage: Target"
- Save → widać go na liście

**Mówisz:**
> "AI to nie wszystko. Manualna kontrola nad każdą encją — Marta zawsze rządzi."

**Cel beat'a:** uspokojenie "co jeśli AI się myli" — control jest u Marty.

---

## Beat 6 — Notifications preview (15s)

**Pokazujesz:** screenshot daily digest email (lub real email do test inbox)

**Mówisz:**
> "Codziennie rano 9:00 Marta dostaje digest na maila. Plus alert gdy commitment przekracza deadline."

**Cel beat'a:** apka działa proaktywnie, nawet gdy Marta nie otwiera.

---

## Beat 7 — Wrap (30s)

**Mówisz:**
> "Marta przestała zapominać obietnice. Widzi wszystko co ją goni — inwestorów, design partnerów, kandydatów — w jednym miejscu. AI robi czarną robotę: czyta notatki, mapuje na osoby, proponuje następny krok.
>
> Zbudowane w 2 tygodnie, 5 osób, stack per-feature. Dataset z 50 prawdziwych relacji w środku.
>
> Pytania?"

---

## Backup plan — co jeśli coś padnie

| Scenariusz | Fallback |
|---|---|
| AI API timeout / 500 | Mamy cached extraction response w `demo-data/cached-extraction.json` — przełączasz flag w env i extraction zwraca z cache |
| DB padło | Lokalna kopia na laptopie z `pnpm dev` — przełącz URL |
| Internet padł | Lokalny screen recording 5-min jako backup wideo (paczka G3 to przygotuje) |
| Pre-prepared paste nie wkleja się | Print z `demo-data/sample-paste.md` na kartce, czytasz na głos i wpisujesz |

## Demo data — pliki do przygotowania

- `demo-data/sample-paste.md` — tekst do wklejenia w Beat 4
- `demo-data/cached-extraction.json` — fallback gdy AI padnie
- `demo-data/email-digest-screenshot.png` — Beat 6
- `demo-data/seed-people.csv` — subset z `dataset-marta.csv` (tylko investor/design_partner/hire)

## Co NIE pokazujemy na demo

- ❌ Kanban (wycięty z MVP)
- ❌ Search / Q&A chatbot (nice-to-have, niestabilny pod presją)
- ❌ Integracje (poza scope'em)
- ❌ Audio upload (poza scope'em)
- ❌ Login screen (single-user, brak autha)

## Próba demo

**Minimum 2 pełne próby przed live demo:**
- Próba #1 — dzień 12 hackathonu — bug bash
- Próba #2 — dzień 13 — z timerem, mierzymy czy mieścimy się w 5 min
- Jeśli nie mieścimy się w 5 min — wycinamy Beat 5 lub Beat 6 (najmniej impact)
