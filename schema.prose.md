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
      dynamics.formal.md
      invariants.formal.md
    prose/
      structure.prose.md
      dynamics.prose.md
      invariants.prose.md
```

For each, the **`*.formal.md`** is its precise, formal statement; the **`*.prose.md`** is the same in plain language. The formal is specified first; the prose is derived from it.

## 2. Formal Specification

We work with **logical models** — the system expressed in formal logic. `structure.formal.md` is in **Description Logic**, `dynamics.formal.md` in **linear temporal logic** (LTLf), and `invariants.formal.md` in **first-order logic**.

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

## 3. Prose Specification

The plain-language mirror of §2, derived from it.