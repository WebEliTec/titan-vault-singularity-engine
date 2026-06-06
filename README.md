# Titan Domain Modeling Framework

Every piece of software models a domain, physical or virtual. Fail to model that domain correctly and you end up with inferior software — no matter how clean your code or how elaborate your software architecture.

In most projects the domain is discovered and developed whilst writing code, so the domain model remains implicit in that code at all times. To reason about it apart from implementation details, a developer or agent must extract it on-the-fly into working memory. That improvised extraction has no canonical reference to measure itself against, and from this single absence each of its errors follows:

- **Incompleteness** — not all domain model properties are recovered from the codebase, and nothing exists against which the extraction's completeness can be measured.
- **Uncorrectable flaws** — malformed or inadequate properties are accepted as "sunk costs", because the model cannot be corrected without rewriting the software that carries it.
- **Software-model contamination** — with no boundary between the two, properties of the software model itself are mistaken for properties of the domain it was meant to model.

The common fact behind all three is that the domain model has no explicit, canonical existence of its own. It lives only dissolved into the code, and must therefore be re-extracted by improvisation every time it is needed.

The Titan Domain Modeling Framework (TDMF) seeks to provide a set of procedures and methodologies to facilitate the modeling of the domain at hand.

It produces a domain model which is prescriptive for the succeeding software development. 

## The model

The model is the domain made explicit: a single artifact, authored before the software and kept apart from it. It is the one authoritative statement of what the domain is. The software conforms to the model, not the model to the software. Where the two disagree, the code is wrong and is corrected, never the model.

A model is composed of three views. The **structural view** fixes what exists: the domain's entities, their properties, and the relations among them. The **invariant view** states what must hold in every state. The **dynamic view** constrains how the domain may change over time. Each view is written in the logic suited to it: the structural view in description logic, the invariant view in first-order logic, the dynamic view in LTLf (linear temporal logic over finite traces).

Each view is written as a formal statement and a prose mirror of it. The formal is canonical — the precise, machine-checkable source of truth; the prose is derived from it, a plain-language rendering for review. In every case the subject is the domain — what the software is about — never the software itself, whose structure (its classes, services, architecture) is a separate concern.

## Versioning

A domain is never modeled correctly in one pass. Understanding deepens, requirements shift, and the domain itself evolves. The model is therefore versioned: it advances as a sequence of self-contained snapshots, corrected and extended one version at a time.

Each version is a directory `v<N>` under `model/`. Inside, the three views — `structure`, `invariants`, `dynamics` — each appear as a formal document and a prose mirror, beside a `STATUS` file that marks the version's stage (see Authoring):

```
model/
  v1/
    STATUS
    formal/
      structure.formal.md
      invariants.formal.md
      dynamics.formal.md
    prose/
      structure.prose.md
      invariants.prose.md
      dynamics.prose.md
  v2/
    …
```

A version is a self-contained snapshot of the model — read its files and you have the model entire. It is a full copy, not a delta from the previous version.

## Authoring a version

### The schema

Read `schema.formal.md` and `schema.prose.md` first: together they define a model document — its grammar and its well-formedness constraints (`W1–W10`). Every formal file must conform to these shapes, and not for style: the formal is machine-readable, so a document that strays will not parse. The schema governs every version.

### The loop

Authoring is a loop. You write the formal domain model — the `*.formal.md` documents — and derive its prose mirror, the `*.prose.md` documents, from it; the human then reviews that prose. You change the formal first; the prose follows, always re-derived from it and never hand-corrected. So when review turns up a fault, you do not touch the prose — you fix the formal and re-derive the prose from it. The formal domain model is the single source of truth, and the prose never drifts from it.

