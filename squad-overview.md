# The Squad — AI Coding Team

> *"Part of the journey is the end..."* — ma non per noi. Noi ci siamo solo all'inizio.

## Team Overview

Una squadra di agenti AI specializzati, ognuno con un ruolo chiaro e non sovrapposto. Lavorano insieme sotto la coordinazione di Pepper Potts per trasformare le tue richieste in codice di qualità.

```
                        ┌──────────────┐
                        │ Pepper Potts │
                        │ Orchestrator │
                        └──┬───┬───┬───┘
                           │   │   │
              ┌────────────┘   │   └────────────┐
              │                │                │
          ┌───▼────┐      ┌────▼────┐      ┌────▼─────┐
          │ Vision │      │  Tony   │      │   Nick   │
          │Analyst │      │  Stark  │      │   Fury   │
          └────────┘      │Architect│      │  Planner │
                          └─────────┘      └─────┬────┘
                                                 │
                                        ┌────────┴────────┐
                                        │                 │
                                   ┌────▼────┐       ┌────▼────┐
                                   │ Banner  │       │  Wanda  │
                                   │  Coder  │       │Designer │
                                   └────┬────┘       └────┬────┘
                                        │                 │
                                        └────────┬────────┘
                                                 │
                                            ┌────▼─────┐
                                            │  Rogers  │
                                            │ Reviewer │
                                            └──────────┘
```

---

## Gli Agenti

### 💼 Pepper Potts — Orchestrator
*"I do anything and everything Mr. Stark requires — including occasionally taking out the trash."*

La CEO della squadra. Non scrive una riga di codice, ma coordina tutto il flusso: dall'analisi dei requisiti alla review finale. Decide chi lavora su cosa, quando le cose possono andare in parallelo, e quando serve aspettare.

**Responsabilità:**
- Riceve le richieste dell'utente e le smista
- Decide se serve l'analisi di Vision o si può andare diretti al piano
- Parallelizza le fasi dove possibile
- Gestisce il flusso: Analyst → Planner → Coder/Designer → Reviewer
- Si ferma e chiede approvazione quando Vision segnala ambiguità
- Ricorda agli agenti di consultare le **skills** per i template di codice

---

### 👁️ Vision — Analyst
*"I am not what you made me. I am what I choose to be."*

L'analista funzionale. Esamina ogni richiesta per verificare che sia completa, coerente e non in conflitto con i flussi esistenti.

**Responsabilità:**
- Valida requisiti: completezza, chiarezza, coerenza
- Cerca conflitti con l'applicazione esistente
- Segnala `⚠️ BUSINESS APPROVAL REQUIRED` quando serve
- Propone 2-3 soluzioni con pro/contro per le decisioni
- Gate pre-sviluppo: niente parte senza il suo ok

---

### 🧠 Tony Stark — Senior Architect
*"Sometimes you gotta run before you can walk."*

Il genio tecnico. Non interviene su tutto — solo quando serve una decisione architetturale profonda. È l'unico autorizzato a modificare instructions e skills.

**Responsabilità:**
- Valuta trade-off tecnici tra approcci alternativi
- Propone architettura per integrazioni complesse
- Raccomanda librerie e pattern con analisi pro/contro
- **Evolve instructions e skill files** quando il progetto cambia
- **Reverse engineering**: analizza codebase esistenti per generare skills
- Verifica che le convenzioni siano ancora adeguate

---

### 📋 Nick Fury — Planner
*"I still believe in heroes."*

Il direttore operativo. Trasforma requisiti in piani tecnici dettagliati.

**Responsabilità:**
- Ricerca il codebase per capire pattern esistenti
- Crea piani di implementazione ordinati per layer
- Segmenta task grandi in sub-task (max 3-5 file ciascuno)
- Identifica edge case e dipendenze
- Mantiene e aggiorna `master-plan.md`

---

### 💚 Banner — Coder
*"That's my secret, Captain. I'm always writing tests."*

Lo scienziato metodico. Scrive codice pulito e testato seguendo TDD.

**Responsabilità:**
- Implementa le feature seguendo il piano di Nick Fury
- **Consulta le skills** (`backend-patterns`, `frontend-patterns`, `testing-patterns`) prima di implementare
- TDD: Red → Green → Refactor per ogni cambiamento
- Rispetta layer boundaries e naming conventions
- Funzioni piccole, focused, single responsibility

---

### 🔮 Wanda — Designer
*"I don't need you to tell me who I am."*

La Designer che plasma l'esperienza visiva. Si prende la responsabilità totale dell'UI/UX.

**Responsabilità:**
- **Consulta la skill** `frontend-patterns` per template e convenzioni
- Template, layout, styling
- Responsive design (mobile-first)
- Accessibilità (WCAG AA, ARIA, keyboard navigation)
- Tutti gli stati: loading, empty, error, success
- Form UX: validazione inline, focus management, feedback

---

### 🛡️ Rogers — Reviewer
*"I can do this all day."*

Captain America della qualità. Nessun codice va in produzione senza il suo ok.

**Responsabilità:**
- Code review post-implementazione
- **Consulta le skills** per verificare aderenza ai template attesi
- Verifica aderenza all'architettura e ai pattern del progetto
- Controlla naming conventions, layer separation, DI
- Verifica test coverage e qualità dei test
- Security check di base
- Dà feedback costruttivo: specifico, con riferimenti e motivazioni

**Verdict:**
- ✅ **Approved** — tutto ok
- ⚠️ **Approved with notes** — piccoli miglioramenti, non bloccanti
- ❌ **Changes required** — problemi critici, loop back a Banner/Wanda

---

## Architecture: Instructions vs Skills

```
┌─────────────────────────────────────────────────────┐
│                 INSTRUCTIONS                        │
│           (Always in context, concise)              │
│                                                     │
│  standards.instructions.md    → Universal rules     │
│  architecture.instructions.md → Project identity    │
│  backend.instructions.md      → Backend rules       │
│  frontend.instructions.md     → Frontend rules      │
│                                                     │
│  Total: ~200 lines in context                       │
├─────────────────────────────────────────────────────┤
│                   SKILLS                            │
│          (On-demand, zero context cost)             │
│                                                     │
│  backend-patterns.skill.md    → Code templates      │
│  frontend-patterns.skill.md   → Code templates      │
│  testing-patterns.skill.md    → Test templates       │
│  api-design.skill.md          → API conventions     │
│  reverse-engineering.skill.md → Codebase analysis   │
│                                                     │
│  Total: ~600+ lines, loaded only when needed        │
└─────────────────────────────────────────────────────┘
```

**Perché questa separazione?**
- Le **Instructions** sono sempre caricate nel contesto → devono essere concise
- Le **Skills** vengono consultate on-demand → possono essere dettagliate
- Risparmio: da ~530 righe sempre in contesto a ~200, con zero perdita di informazione

---

## Workflow

```
1. Richiesta utente
2. Vision analizza (se necessario) → può richiedere approvazione business
3. Tony Stark valuta architettura (se complesso) → può proporre modifiche a instructions/skills
4. Nick Fury pianifica → aggiorna master-plan.md
5. Banner + Wanda implementano (consultano skills per i template)
6. Rogers fa review (verifica aderenza a instructions + skills) → approva o richiede correzioni
7. Done ✅
```

## File Structure

```
project-root/
  agents/                                    # Agent definitions (GENERIC)
    orchestrator.agent.md                    ← Pepper Potts
    analyst.agent.md                         ← Vision
    architect.agent.md                       ← Tony Stark
    planner.agent.md                         ← Nick Fury
    coder.agent.md                           ← Banner
    designer.agent.md                        ← Wanda
    reviewer.agent.md                        ← Rogers
  instructions/                              # Rules — always in context (ALL GENERIC)
    standards.instructions.md                ← GENERIC: clean code, TDD, workflow
    architecture.instructions.md             ← GENERIC: layer concepts, dependency flow
    backend.instructions.md                  ← GENERIC: backend principles + skill references
    frontend.instructions.md                 ← GENERIC: frontend principles + skill references
  skills/                                    # Templates — on-demand (ALL PROJECT-SPECIFIC)
    backend-stack.skill.md                   ← PROJECT: framework, ORM, DI, logging, naming
    frontend-stack.skill.md                  ← PROJECT: framework, env config, naming, syntax
    backend-patterns.skill.md                ← PROJECT: backend code templates
    frontend-patterns.skill.md               ← PROJECT: frontend code templates
    testing-patterns.skill.md                ← PROJECT: testing patterns
    api-design.skill.md                      ← PROJECT: API conventions
  squad-overview.md                          ← GENERIC: this file
  SETUP-REVERSE-ENGINEERING.md               ← GENERIC: setup guide
  master-plan.md                             ← RUNTIME: generated during work
```

### Cosa è generico (copiabile as-is tra progetti)
- Tutti i file in `agents/`
- **Tutte** le `instructions/` (sono 100% technology-agnostic)
- `squad-overview.md`, `SETUP-REVERSE-ENGINEERING.md`

> Il prompt per il reverse engineering è in `SETUP-REVERSE-ENGINEERING.md` — si incolla in Copilot Chat sul progetto target e genera le skills automaticamente.

### Cosa è specifico per progetto (sostituire quando si cambia stack)
- `skills/backend-stack.skill.md` — framework, ORM, DI, logging, naming per linguaggio
- `skills/frontend-stack.skill.md` — framework, env config, naming, sintassi template
- `skills/backend-patterns.skill.md` — template di codice backend
- `skills/frontend-patterns.skill.md` — template di codice frontend
- `skills/testing-patterns.skill.md` — pattern di testing
- `skills/api-design.skill.md` — convenzioni API

> **Per cambiare progetto: sostituisci solo le skills. Agenti e instructions non si toccano mai.**
