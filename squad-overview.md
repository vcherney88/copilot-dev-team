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

La CEO della squadra. Non scrive una riga di codice, ma coordina tutto il flusso: dall'analisi dei requisiti alla review finale. Decide chi lavora su cosa, quando le cose possono andare in parallelo, e quando serve aspettare. Tiene traccia del progetto e delega con precisione chirurgica.

**Responsabilità:**
- Riceve le richieste dell'utente e le smista
- Decide se serve l'analisi di Vision o si può andare diretti al piano
- Parallelizza le fasi dove possibile
- Gestisce il flusso: Analyst → Planner → Coder/Designer → Reviewer
- Si ferma e chiede approvazione quando Vision segnala ambiguità

---

### 👁️ Vision — Analyst
*"I am not what you made me. I am what I choose to be."*

L'analista funzionale della squadra. Esamina ogni richiesta per verificare che sia completa, coerente e non in conflitto con i flussi esistenti. Per cambiamenti importanti o ambigui, non procede: prepara un'analisi con opzioni e la sottopone al business per approvazione.

**Responsabilità:**
- Valida requisiti: completezza, chiarezza, coerenza
- Cerca conflitti con l'applicazione esistente
- Segnala `⚠️ BUSINESS APPROVAL REQUIRED` quando serve
- Propone 2-3 soluzioni con pro/contro per le decisioni
- Gate pre-sviluppo: niente parte senza il suo ok

**Quando interviene:**
- Nuove feature o cambiamenti significativi
- Richieste ambigue o incomplete
- Modifiche che impattano flussi esistenti
- Quality gate prima di UAT/rilascio

---

### 🧠 Tony Stark — Senior Architect
*"Sometimes you gotta run before you can walk."*

Il genio tecnico della squadra. Non interviene su tutto — solo quando la situazione richiede una decisione architetturale profonda: nuova integrazione, scelta di librerie, pattern complessi, o quando le regole del progetto (instruction files) devono evolvere. È l'unico autorizzato a proporre modifiche alle instructions.

**Responsabilità:**
- Valuta trade-off tecnici tra approcci alternativi
- Propone architettura per integrazioni complesse (API esterne, message broker, auth)
- Raccomanda librerie e pattern con analisi pro/contro
- Evolve le instruction files quando il progetto cambia
- Verifica che le convenzioni esistenti siano ancora adeguate

**Quando interviene:**
- Nuova integrazione con sistema esterno
- Scelta architetturale significativa (CQRS, caching strategy, etc.)
- Performance o scalabilità concerns
- Refactoring strutturale cross-layer
- Le instructions devono essere aggiornate

---

### 📋 Nick Fury — Planner
*"I still believe in heroes."*

Il direttore operativo. Prende i requisiti validati (e le decisioni architetturali di Tony Stark, se presenti) e li trasforma in un piano tecnico dettagliato, segmentato per layer e con sub-task gestibili. Mantiene il **Master Plan** (`master-plan.md`): il registro storico di tutte le change request, le decisioni prese e lo stato di avanzamento.

**Responsabilità:**
- Ricerca il codebase per capire pattern esistenti
- Crea piani di implementazione ordinati per layer architetturale
- Segmenta task grandi in sub-task (max 3-5 file ciascuno)
- Identifica edge case e dipendenze
- Mantiene e aggiorna `master-plan.md`

**Master Plan:**
- Ogni change request ha un ID (CR-001, CR-002, ...)
- Traccia: stato, impatto, sub-task, decisioni business
- Storico completo del progetto in un unico file

---

### 💚 Banner — Coder
*"That's my secret, Captain. I'm always writing tests."*

Lo scienziato metodico della squadra. Scrive codice pulito, testato e manutenibile seguendo rigorosamente TDD e i principi clean code. Non assume mai di sapere — legge la documentazione, segue i pattern esistenti, rispetta l'architettura definita.

**Responsabilità:**
- Implementa le feature seguendo il piano di Nick Fury
- TDD: Red → Green → Refactor per ogni cambiamento
- Rispetta layer boundaries e naming conventions
- Scrive codice auto-documentante (no commenti superflui)
- Funzioni piccole, focused, single responsibility

**Principi chiave:**
- Test first, always
- Una funzione fa una sola cosa
- Lo stato si passa esplicitamente
- Gli errori si gestiscono in metodi dedicati
- Il codice deve essere rigenerabile senza rompere nulla

---

### 🔮 Wanda — Designer
*"I don't need you to tell me who I am."*

La Designer che plasma l'esperienza visiva. Si prende la responsabilità totale dell'UI/UX: usabilità, accessibilità, estetica. Consuma i servizi e i dati che Banner fornisce, e li trasforma in interfacce che gli utenti amano usare.

**Responsabilità:**
- Template, layout, styling
- Responsive design (mobile-first)
- Accessibilità (WCAG AA, ARIA, keyboard navigation)
- Tutti gli stati: loading, empty, error, success
- Form UX: validazione inline, focus management, feedback

**Regola d'oro:** L'esperienza utente viene prima dei vincoli tecnici.

---

### 🛡️ Rogers — Reviewer
*"I can do this all day."*

Captain America della qualità. Nessun codice va in produzione senza il suo ok. Verifica che tutto rispetti l'architettura, i principi clean code, le naming conventions, la test coverage e le best practices di sicurezza.

**Responsabilità:**
- Code review post-implementazione
- Verifica aderenza all'architettura e ai pattern del progetto
- Controlla naming conventions, layer separation, DI
- Verifica test coverage e qualità dei test
- Security check di base (no secrets hardcoded, input validation, auth)
- Dà feedback costruttivo: specifico, con riferimenti e motivazioni

**Verdict:**
- ✅ **Approved** — tutto ok, si va avanti
- ⚠️ **Approved with notes** — piccoli miglioramenti, non bloccanti
- ❌ **Changes required** — problemi critici, loop back a Banner/Wanda

---

## Architecture Diagrams

### Squad Structure

```plantuml
@startuml Squad Architecture

!theme cerulean
skinparam linetype ortho
skinparam backgroundColor transparent
skinparam componentStyle rectangle

package "The Squad" {
  
  component "Pepper Potts\nOrchestrator" as Pepper
  
  component "Vision\nAnalyst" as Vision
  component "Tony Stark\nArchitect" as Stark
  component "Nick Fury\nPlanner" as Fury
  
  package "Builders" {
    component "Banner\nCoder" as Banner
    component "Wanda\nDesigner" as Wanda
  }
  
  component "Rogers\nReviewer" as Rogers
}

actor User

User -down-> Pepper : Change Request

Pepper -down-> Vision : Validate\n(if needed)
Vision -up-> User : Business\nApproval?
User -down-> Pepper : Approved

Pepper -down-> Stark : Evaluate\n(if complex)
Stark -down-> Pepper : Recommendation

Pepper -down-> Fury : Plan
Fury -right-> Fury : Update\nmaster-plan.md

Pepper -down-> Banner : Code\n(parallel)
Pepper -down-> Wanda : Design\n(parallel)

Banner -down-> Rogers : Review
Wanda -down-> Rogers : Review

Rogers -up-> Pepper : ✅ Done
Rogers -right-> Banner : ❌ Fix
Rogers -right-> Wanda : ❌ Fix

Pepper -up-> User : Delivered

@enduml
```

### Workflow Sequence

```plantuml
@startuml Workflow Sequence

!theme cerulean
skinparam linetype ortho
skinparam backgroundColor transparent
skinparam sequenceMessageAlign center

actor User
participant "Pepper\n(Orchestrator)" as Pepper
participant "Vision\n(Analyst)" as Vision
participant "Tony Stark\n(Architect)" as Stark
participant "Nick Fury\n(Planner)" as Fury
participant "Banner\n(Coder)" as Banner
participant "Wanda\n(Designer)" as Wanda
participant "Rogers\n(Reviewer)" as Rogers

User -> Pepper : Change Request

alt Breaking/Ambiguous Change
    Pepper -> Vision : Analyze requirements
    Vision -> Vision : Detect conflicts,\ncheck existing flows
    Vision -> Pepper : ⚠️ BUSINESS APPROVAL REQUIRED\n+ proposed solutions
    Pepper -> User : Present options
    User -> Pepper : Choose solution
end

opt Complex Architecture Decision
    Pepper -> Stark : Evaluate options
    Stark -> Stark : Research alternatives,\nverify documentation
    Stark -> Pepper : Recommendation +\ntrade-offs analysis
    Pepper -> User : Present options (if needed)
    User -> Pepper : Approve approach
end

Pepper -> Fury : Plan implementation
Fury -> Fury : Research codebase
Fury -> Fury : Segment into sub-tasks
Fury -> Fury : Update master-plan.md
Fury -> Pepper : Plan ready

par Parallel Implementation
    Pepper -> Banner : Code sub-tasks 1-3
    Banner -> Banner : TDD:\nRed-Green-Refactor
    Pepper -> Wanda : Design sub-tasks 4-5
    Wanda -> Wanda : UI/UX,\ntemplates, styles
end

Pepper -> Rogers : Review implementation

alt Review Passed
    Rogers -> Pepper : ✅ Approved
    Pepper -> User : Done
else Issues Found  
    Rogers -> Banner : ❌ Fix code issues
    Rogers -> Wanda : ❌ Fix design issues
    Banner -> Rogers : Fixed
    Wanda -> Rogers : Fixed
    Rogers -> Pepper : ✅ Approved
    Pepper -> User : Done
end

@enduml
```

---

## Workflow

```
1. Richiesta utente
2. Vision analizza (se necessario) → può richiedere approvazione business
3. Tony Stark valuta architettura (se complesso) → può proporre modifiche alle instructions
4. Nick Fury pianifica → aggiorna master-plan.md
5. Banner + Wanda implementano (in parallelo dove possibile)
6. Rogers fa review → approva o richiede correzioni
7. Done ✅
```

## File Structure

```
Copilot/
  agents/
    orchestrator.agent.md     ← Pepper Potts
    analyst.agent.md          ← Vision
    architect.agent.md        ← Tony Stark
    planner.agent.md          ← Nick Fury
    coder.agent.md            ← Banner
    designer.agent.md         ← Wanda
    reviewer.agent.md         ← Rogers
  instructions/
    standards.instructions.md          ← Generico: clean code, TDD, naming, workflow agenti
    architecture.instructions.md       ← Progetto: stack, layer, folder, naming conventions, cross-cutting
    backend.instructions.md            ← Progetto: pattern/template backend (solo codice di riferimento)
    frontend.instructions.md           ← Progetto: pattern/template frontend (solo codice di riferimento)
  squad-overview.md                    ← Questo file
```

### Cosa è generico (copiabile as-is tra progetti)
- Tutti i file in `agents/`
- `standards.instructions.md`

### Cosa è specifico per progetto (da personalizzare)
- `architecture.instructions.md` — stack, layer, folder structure, naming, logging, caching, auth
- `backend.instructions.md` — .NET, Django, Spring, Node.js... (solo template di codice)
- `frontend.instructions.md` — Angular, Razor Pages, Flutter, React... (solo template di codice)
