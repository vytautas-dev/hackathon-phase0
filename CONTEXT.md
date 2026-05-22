# Product Context

## Persona — Marta

Founderka w trakcie **seed roundu**. Technical background (poznała co-foundera Jakuba na Poznań University of Technology). Prowadzi B2B SaaS startup. Jeździ jednocześnie między fundraisingiem, design partner outreach i hiringiem.

Pełna persona: [FigJam](https://www.figma.com/board/clFqkDym18DmfWOFlIc2Bk/Hackathon-2026?node-id=13-5733)

## Problem — co boli Martę

W jej datasecie (`dataset-marta.csv`) widać konkretny, powtarzalny ból:

- *"I told her I'd send the one-pager on Monday. That was **three Mondays ago**"* — Aneta (PKO BP)
- *"URGENT: I need to send her cohort analysis by EOD Thursday. **Don't drop this**"* — Anna (lead investor)
- *"Paweł was supposed to intro me. I should check if he did — it's been two weeks"* — Beata (target VC)
- *"I owe him a reply from August"* — James Chen
- *"told him I'd send by Friday last week and I still haven't"* — Michał (committed angel)
- *"I told her I'd get back to her within two weeks — it's been six"* — Maria (hire candidate)

**Symptomy:**
1. Obietnice się gubią — Marta zapomina co komu obiecała, dropuje high-value leads
2. Stage'y procesu siedzą w głowie — nie wiadomo kto jest "warm", kto "diligence", kto "passed"
3. Intra nie są dopilnowane (Paweł→Beata, Klaus→Tobias) — wiszą bez follow-upu
4. Cross-category mental switching (fundraising / sales / hiring) gubi kontekst

## Value proposition

> CRM dla business opportunities **który sam się aktualizuje** z wrzuconych notatek/PDFów/transkryptów. AI ekstrahuje osoby, stage, commitments. Marta widzi **"co dziś"** w jednym miejscu i nie gubi nic.

Mocna teza: Marta nie chce kolejnego Notion/Airtable. Chce **command center** który czyta jej notatki i mówi co zrobić.

## Scope

### W zakresie MVP

**Encje:**
- People w **3 kategoriach**: `investor`, `design_partner`, `hire`
- **3 osobne pipeline'y** per kategoria, każdy ze **swoimi stage'ami**
- Stage'y są **dynamiczne** — AI może zaproponować dodanie / usunięcie / branch
- **Commitments** jako first-class entity (dwukierunkowe: `i_owe` / `they_owe`)
- **Interactions** — timeline rozmów, maili, spotkań

**Funkcje (must-have):**

1. **AI Extraction** — paste tekstu → osoby, stage updates, commitments (z confidence + review queue)
2. **Today / Inbox view** — co dziś + co zaległe (cross-category)
3. **Person Profile** — timeline interakcji, lista commitments, stage history, notes
4. **AI Next-step Suggestions** — z action buttons (mailto: draft, .ics, copy to clipboard)
5. **Manual edit** — full CRUD na person / commitment / stage (AI to nie wszystko)
6. **Notifications** — email przez Resend (daily digest 9am, overdue alerts)

**Funkcje (nice-to-have, jeśli czas):**

7. **Search / Q&A chatbot** — "kiedy ostatnio rozmawiałam z Beatą?", "kto czeka na ode mnie?"

### Poza zakresem MVP

- Inne kategorie z CSV (family, friends, neighbors, ex-colleagues etc. — istnieją w datasecie ale poza scope'em)
- Kanban view (świadomie wycięty)
- Multi-user / teams / auth
- Integracje z Google Workspace / Slack / Atlassian / Notion / Calendar (stretch task `S1`)
- Audio input / Whisper (stretch task `S2`)
- Native push notifications
- Mobile apps

## Success criteria

- ✅ Hosted prototyp na publicznym URL
- ✅ Klikalny przez jurorów (z seed data z `dataset-marta.csv`)
- ✅ Live demo wg `DEMO.md` — wykonalny w 5 min, end-to-end
- ✅ Apka faktycznie wykonuje AI extraction na świeżym wkleju (nie jest hardcoded)

## Anti-goals

- ❌ Pokazanie 30 feature'ów na demo — lepiej **3 zrobione dobrze**
- ❌ Pre-canned demo data tak, że paste-extract to fake — **musi działać na świeżym wkleju**
- ❌ Zachwyt nad UI bez działającej logiki AI — to nie produkt
- ❌ "Generic CRM with AI bolted on" — wartość jest w **automatycznej aktualizacji** stanu, nie w polach formularzy
