# Model

The Singularity Engine's **prescriptive model**: the single, authoritative statement of what the system *is*. The model is written here first, and the code is brought into conformance with it — never the reverse. Everything the running system does is a derivation of what is written here.

## 1. Versioning

The model is **living**: it evolves through **version directories** under `model/`, each holding one version of the model. The schema (this file and `schema.formal.md`) lives at the vault root, outside `model/`.

```
titan-vault/
  README.md
  schema.formal.md
  schema.prose.md
  reference/
  model/
    <version>/
      formal/
        structure.formal.md
        invariants.formal.md
        dynamics.formal.md
      prose/
        structure.prose.md
        invariants.prose.md
        dynamics.prose.md
```

For each, the **`*.formal.md`** is its precise, formal statement; the **`*.prose.md`** is the same in plain language. The formal is specified first; the prose is derived from it.

## 2. Formal Specification

We work with **logical models** — the system expressed in formal logic. `structure.formal.md` is in **Description Logic**, `invariants.formal.md` in **first-order logic**, and `dynamics.formal.md` in **linear temporal logic** (LTLf).

### 2.1 structure.formal.md

Specifies **what exists**. It starts with a `# Structure` main-heading and follows this invariant heading scheme:

```
# Structure
## Concepts
## Taxonomy
## Properties
## Relations
```

#### 2.1.1 Concepts

Declares the signature: every concept named once — the **entities** and the **enumerated value types**. An entity is an atomic concept `C`; an enumerated value type is fixed by its members: `E ≡ {v₁, …, vₙ}`.

#### 2.1.2 Taxonomy

Declares the **is-a hierarchy** over the concepts; a subconcept inherits its parent's properties and relations. Each link is a subsumption: `C ⊑ D` ("every C is a D").

#### 2.1.3 Properties

Declares each concept's own data fields, each named once within that concept. Every field carries:

- a **type** — a primitive datatype (`String`, `Integer`, `Boolean`, `ID`) or an enumerated value type from `## Concepts`, never an entity (entity links live in `## Relations`);
- a **cardinality** — `=1` (required), `≤1` (optional), or `≥1` (multi-valued); in general a `Number` bound `=k`, `≤k`, `≥k`.

Each is a typed, cardinality-restricted role on its concept — a `Number` bound on the field: `Concept ⊑ =1 field.Type`.

#### 2.1.4 Relations

Declares the **domain relations** — the associative (non-taxonomic) links between concepts. Every relation carries:

- a **domain** and **range** — its source and target concepts;
- a **cardinality on each side** — the range count per domain instance, and the domain count per range instance (each `1`, `0..1`, `1..N`, or `0..N`).

Each is declared once, the multiplicity sitting beside the concept it counts: `role : Domain [d] → [r] Range` — `[r]` is the range count per domain instance, `[d]` the domain count per range instance. For example `r : A [0..1] → [1..N] B` reads: each `A` relates to **1..N** `B` (the range count `[r]`); each `B` relates to **0..1** `A` (the domain count `[d]`).

#### 2.1.5 Example

A minimal `structure.formal.md`:

```
# Structure

## Concepts

EntityClass, AttributeSetVersion, Attribute, TraitAttribute
VersionStatus ≡ {draft, committed}

## Taxonomy

TraitAttribute ⊑ Attribute

## Properties

EntityClass          ⊑ =1 name.String
AttributeSetVersion  ⊑ =1 version.Integer ⊓ =1 status.VersionStatus
Attribute            ⊑ =1 key.String ⊓ =1 required.Boolean ⊓ ≤1 maxLength.Integer

## Relations

hasAttributeSetVersion : EntityClass [1] → [0..N] AttributeSetVersion
hasAttribute           : AttributeSetVersion [1] → [0..N] Attribute
```

### 2.2 invariants.formal.md

Specifies **what must always be true**. Where `structure.formal.md` says what *can* exist, `invariants.formal.md` rules out the arrangements that must *never* occur — in **any** single state of the system.

It starts with a `# Invariants` main-heading followed by a flat list of entries. Unlike `structure.formal.md` there are no sub-sections: every entry is the same kind of statement, so one list suffices.

```
# Invariants

<Name>  <first-order sentence>
<Name>  <first-order sentence>
…
```

Each entry is a **named first-order sentence**:

- a **name** — a stable numbered id (`I1`, `I2`, …), so the invariant can be cited from prose and code;
- a **sentence** — a *closed* first-order formula (every variable quantified) that must hold in every state.

The formulas range over the vocabulary `structure.formal.md` declares: each **concept** is a unary predicate `C(x)` ("x is a C"), each **relation** a binary predicate `r(x, y)`, and each **property** a function `f(x)` when it is required (`=1`), or a binary predicate `f(x, v)` when optional or multi-valued. The structure's signature *is* the invariants' vocabulary — the bridge between the two layers.

#### 2.2.1 Example

Two invariants over the `structure.formal.md` example above:

```
# Invariants

I1  ∀ v, a1, a2.  hasAttribute(v, a1) ∧ hasAttribute(v, a2) ∧ key(a1) = key(a2)  →  a1 = a2
I2  ∀ e, v1, v2.  hasAttributeSetVersion(e, v1) ∧ hasAttributeSetVersion(e, v2) ∧ version(v1) = version(v2)  →  v1 = v2
I3  ∀ v.  AttributeSetVersion(v) → version(v) ≥ 1
I4  ∀ a, n.  maxLength(a, n) → n > 0
```

`I1` — within one attribute-set version, no two attributes share a key. `I2` — within one entity class, no two attribute-set versions share a version number. `I3` — every attribute-set version's number is at least 1 (a *required* `Integer` property, used as a function). `I4` — an attribute's max length, if set, is positive (an *optional* `Integer` property, used as a binary predicate — the value variable `n` is `Integer`-typed).

### 2.3 dynamics.formal.md

Specifies **what happens over time**. Where `invariants.formal.md` constrains a single state, `dynamics.formal.md` constrains *sequences* of states — a run of the system — ruling out the histories that must never occur. Its formulas use the structure's predicates plus four temporal operators:

- `G φ` — *globally*: φ holds at every instant;
- `F φ` — *finally*: φ holds at some later instant;
- `X φ` — *next*: φ holds at the following instant;
- `φ U ψ` — *until*: φ holds until ψ does.

Formulas may also quantify over domain elements (`∀v`, `∃a`) — *first-order* temporal logic, the data-aware form. Dynamics introduces **no new primitives**: every formula is built from the structure's vocabulary and these operators. It starts with a `# Dynamics` main-heading and follows this invariant heading scheme:

```
# Dynamics
## Events
## Templates
## Rules
```

`## Events` and `## Templates` are *definitions* (`name ≝ formula`) — abbreviations that keep the rules concise and drive the prose rendering; `## Rules` are the constraints themselves. Vocabulary first, then constraints — the same shape as `structure.formal.md`. All definitions must be **acyclic**, so every name expands away to raw LTLf over the structure.

#### 2.3.1 Events

Named state-changes — the things the system can *do* — each a definition whose parameters are **terms** (domain elements):  `commit(v) ≝ status(v) = draft ∧ X status(v) = committed`. Domain-specific.

#### 2.3.2 Templates

Named rule-shapes, each a definition whose parameters are **formulas**:  `Response(a, b) ≝ G(a → F b)`. These are the subset of standard DECLARE templates this version uses — the full catalogue is the lookup menu in [`reference/declare-templates.md`](reference/declare-templates.md), copy in only what's used — plus any **custom** shapes we coin, e.g. `Monotone(p) ≝ G(p → G p)`.

#### 2.3.3 Rules

A flat list of **named temporal formulas** (`D1`, `D2`, …) — the constraints on the system's runs — written using the events and templates above. Like invariants, every rule is **closed** — every variable quantified. In `dynamics.formal.md` the rules use the names directly; `dynamics.prose.md` renders the same rules with a plain-English gloss.

#### 2.3.4 Example

```
# Dynamics

## Events

commit(v)           ≝  status(v) = draft ∧ X status(v) = committed
addAttribute(v, a)  ≝  ¬ hasAttribute(v, a) ∧ X hasAttribute(v, a)

## Templates

Monotone(p)         ≝  G( p → G p )

## Rules

D1  ∀v. Monotone( status(v) = committed )
D2  ∀v. G( commit(v) → G ¬∃a. addAttribute(v, a) )
```

`D1` — once committed, a version stays committed (the "never returns to draft" rule; temporal, so it lives here, not in `invariants.formal.md`), stated with the `Monotone` template. `D2` — committing freezes the attribute set: a committed version never gains an attribute.

## 3. Prose Specification

Each `*.prose.md` is the plain-language mirror of its `*.formal.md`, derived from it and serving as the human's review surface. The contract:

- it mirrors the formal's **headings** one-to-one;
- it carries **one entry per formal id** — each concept, field, relation, `I#`, `D#` — under the same id;
- it **adds and drops nothing**: every formal statement appears, and no constraint is invented;
- it renders relation multiplicities per §2.1.4, and rules with their template label plus a plain-language gloss.

The formal is canonical; the prose is re-derived and re-checked after **any** formal edit. Drift between the tiers is a defect, not a variant.