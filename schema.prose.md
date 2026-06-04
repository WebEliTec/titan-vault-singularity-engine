# Model

The Singularity Engine's **prescriptive model**: the single, authoritative statement of what the system *is*. The model is written here first, and the code is brought into conformance with it — never the reverse. Everything the running system does is a derivation of what is written here.

## 1. Versioning

The model is **living**: it evolves through **version directories**, each holding one version of the model.

```
model/
  README.md
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

Declares each concept's own data fields. Every field carries:

- a **type** — a primitive datatype (`String`, `Integer`, `Boolean`, `ID`) or an enumerated value type from `## Concepts`;
- a **cardinality** — `1`, `0..1`, `1..N`, or `0..N` (subsuming *required* vs *optional*).

Each is a typed, cardinality-restricted role on its concept: `Concept ⊑ (n) field.Type`.

#### 2.1.4 Relations

Declares the **domain relations** — the associative (non-taxonomic) links between concepts. Every relation carries:

- a **domain** and **range** — its source and target concepts;
- a **cardinality on each side** — the range count per domain instance, and the domain count per range instance (each `1`, `0..1`, `1..N`, or `0..N`).

Each is declared once, the multiplicity sitting beside the concept it counts: `role : Domain [d] → [r] Range` — `[r]` is the range count per domain instance, `[d]` the domain count per range instance.

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
Attribute            ⊑ =1 key.String ⊓ =1 required.Boolean

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

I1  ∀ v, a₁, a₂.  hasAttribute(v, a₁) ∧ hasAttribute(v, a₂) ∧ key(a₁) = key(a₂)  →  a₁ = a₂
I2  ∀ e, v₁, v₂.  hasAttributeSetVersion(e, v₁) ∧ hasAttributeSetVersion(e, v₂) ∧ version(v₁) = version(v₂)  →  v₁ = v₂
```

`I1` — within one attribute-set version, no two attributes share a key. `I2` — within one entity class, no two attribute-set versions share a version number.

## 3. Prose Specification

The plain-language mirror of §2, derived from it.