# Superior Byte Works Wrighter

Superior Byte Works Wrighter is the repository's structured writing system. It is the skill pack that turns raw ideas, research, diagrams, and delivery targets into usable artifacts: technical documents, guides, reports, visual explainers, offline deliverables, and domain-specific writing workflows.

If `my-farm-advisor` is the farm brain, `wrighter` is the documentation and publishing engine that explains what the system does, how it works, and how to hand it off clearly.

## What Wrighter Does

- Provides a text-first writing workflow so documentation exists before implementation polish.
- Organizes writing into reusable domains: stories, visuals, notation, prose, discovery, craft, and schematics.
- Supplies Mermaid-heavy visual guidance, SVG patterns, and delivery packaging for offline or portable outputs.
- Supports research intake and synthesis before drafting so documents start from evidence rather than vibes.
- Gives this repo a reusable way to build serious documentation instead of one-off markdown files.

## The Wrighter Model

```mermaid
flowchart LR
    accTitle: Wrighter Three-Phase Model
    accDescr: Three-phase writing workflow showing mandatory text-first work followed by optional structured artifacts and optional visual or delivery polish.

    phase_1([📝 Phase 1: text first]) --> phase_2[📊 Phase 2: charts, code, or structure]
    phase_1 --> phase_3[🎨 Phase 3: visuals or delivery polish]
    phase_2 --> phase_3

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef secondary fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f

    class phase_1 primary
    class phase_2,phase_3 secondary
```

Wrighter is opinionated about order:

1. Write the thing clearly.
2. Add structure, code, charts, or validation if needed.
3. Add richer visuals or delivery packaging only after the text is solid.

That is why the core skill is useful in this repo: it keeps operational docs, walkthroughs, reports, and references readable before they become fancy.

## How It Is Organized

| Area       | Purpose                                                      | Start here                                            |
| ---------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| Core       | Principles, workflow, and system model                       | [`core/index.md`](core/index.md)                      |
| Stories    | Long-form documents, references, and template-driven writing | [`stories/INDEX.md`](stories/INDEX.md)                |
| Visuals    | Mermaid, SVG, MIDI, and diagram-authoring guidance           | [`visuals/INDEX.md`](visuals/INDEX.md)                |
| Notation   | Math, theorem, and precision-oriented writing support        | [`notation/INDEX.md`](notation/INDEX.md)              |
| Prose      | Style, structure, and domain writing patterns                | [`prose/INDEX.md`](prose/INDEX.md)                    |
| Discovery  | Research, synthesis, citation, and review intake             | [`discovery/INDEX.md`](discovery/INDEX.md)            |
| Craft      | Validation and quality-control helpers                       | [`craft/INDEX.md`](craft/INDEX.md)                    |
| Schematics | AI-assisted visual generation guidance                       | [`schematics/INDEX.md`](schematics/INDEX.md)          |
| Delivery   | Offline HTML and delivery packaging patterns                 | `delivery/` guides linked from [`SKILL.md`](SKILL.md) |

## How Requests Flow Through Wrighter

```mermaid
flowchart TD
    accTitle: Wrighter Domain Routing
    accDescr: Routing flow that starts from a writing task, chooses the right Wrighter domain, and produces a document or other writing artifact.

    request([🖋️ Writing task]) --> router[🧭 Wrighter skill router]
    router --> domain_choice{Choose domain}

    subgraph domains ["📚 Wrighter domains"]
        direction TB
        stories[Stories]
        visuals[Visuals]
        notation[Notation]
        prose[Prose]
        discovery[Discovery]
        craft[Craft]
        schematics[Schematics]
    end

    domain_choice --> domains
    domains --> artifact([✅ Document or delivery artifact])

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef outcome fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d

    class request,router,domain_choice primary
    class artifact outcome
```

This means Wrighter is not one narrow README-writing tool. It is a layered documentation system with routing, standards, and downstream artifact support.

## Mermaid and Visual Workflow

Wrighter has a strong visual-authoring layer, especially for Mermaid.

```mermaid
flowchart TD
    accTitle: Mermaid Visual Selection Flow
    accDescr: Diagram showing how Wrighter moves from an idea to the right Mermaid family and then into the style-guided final visual.

    idea([💡 Concept to explain]) --> choose_type{Pick the diagram family}
    choose_type --> flowchart_choice[Flowchart]
    choose_type --> sequence_choice[Sequence]
    choose_type --> architecture_choice[C4 or architecture]
    choose_type --> state_choice[State]
    choose_type --> journey_choice[User journey]
    choose_type --> other_choice[Other Mermaid families]
    flowchart_choice --> style_guide[📏 Style guide and examples]
    sequence_choice --> style_guide
    architecture_choice --> style_guide
    state_choice --> style_guide
    journey_choice --> style_guide
    other_choice --> style_guide
    style_guide --> final_visual([✅ Readable final visual])

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef support fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f
    classDef outcome fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d

    class idea,choose_type primary
    class style_guide support
    class final_visual outcome
```

Key visual entry points:

- Mermaid guide: [`visuals/mermaid/GUIDE.md`](visuals/mermaid/GUIDE.md)
- Mermaid style rules: [`visuals/mermaid/style-guide.md`](visuals/mermaid/style-guide.md)
- SVG guide: [`visuals/svg/GUIDE.md`](visuals/svg/GUIDE.md)
- Visual domain overview: [`visuals/INDEX.md`](visuals/INDEX.md)

## Discovery and Research Workflow

```mermaid
flowchart LR
    accTitle: Discovery To Draft Workflow
    accDescr: Research workflow that moves from a topic through search, synthesis, drafting, review, and final delivery.

    intake([🔎 Start with the topic]) --> search[Search strategy]
    search --> gather[Collect sources]
    gather --> synthesize[Synthesize evidence]
    synthesize --> draft[Draft the document]
    draft --> validate[🧪 Validate and review]
    validate --> deliver([📦 Deliver the final package])

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef process fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f
    classDef outcome fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d

    class intake primary
    class search,gather,synthesize,draft,validate process
    class deliver outcome
```

This is one of the most important parts of Wrighter for this repo. It gives the project a repeatable way to move from research to clear documentation instead of skipping straight to prose.

Useful discovery entry points:

- [`discovery/INDEX.md`](discovery/INDEX.md)
- [`discovery/GUIDE.md`](discovery/GUIDE.md)
- [`discovery/search-strategy.md`](discovery/search-strategy.md)
- [`discovery/citation-management.md`](discovery/citation-management.md)

## Delivery Modes

Wrighter also includes delivery-oriented patterns for packaging content once it is written.

- offline open HTML delivery
- sealed or fingerprinted HTML delivery
- shared delivery asset and snapshot models

Those modes are described from the root skill entrypoint in [`SKILL.md`](SKILL.md), because delivery is a downstream concern after the content itself is correct.

## Why It Matters In This Repo

```mermaid
flowchart TD
    accTitle: Wrighter Role In The Repo
    accDescr: Diagram showing how the repo uses Wrighter to produce installation docs, walkthroughs, structured reports, visuals, and delivery-ready documentation assets.

    repo([🌾 My Farm Advisor repo]) --> wrighter[🖋️ Wrighter skill layer]
    repo --> farm_skill[🌱 Farm skill layer]
    wrighter --> docs[Install docs and walkthroughs]
    wrighter --> reports[Structured reports and guides]
    wrighter --> visuals[Mermaid and SVG explanations]
    wrighter --> delivery[Offline delivery patterns]

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef support fill:#ecfccb,stroke:#65a30d,stroke-width:2px,color:#365314

    class repo,wrighter primary
    class farm_skill,docs,reports,visuals,delivery support
```

Wrighter is how this repo explains itself. It is the reason the Cloudflare walkthrough, install docs, skill documentation, and future operator handoffs can be treated as durable assets instead of temporary notes.

## Start Here

- Root skill definition: [`SKILL.md`](SKILL.md)
- Core workflow: [`core/index.md`](core/index.md)
- Visuals overview: [`visuals/INDEX.md`](visuals/INDEX.md)
- Discovery overview: [`discovery/INDEX.md`](discovery/INDEX.md)
- Shared conventions: [`_shared/conventions.md`](_shared/conventions.md)
- Provenance: [`provenance.md`](provenance.md)

## Practical Use In This Repo

Use Wrighter when you need to:

- write or improve installation docs
- produce walkthroughs or operating guides
- add Mermaid diagrams to explain systems clearly
- turn research into structured reports
- package documentation for offline or deliverable-ready use
- keep writing consistent across many domains without losing rigor
