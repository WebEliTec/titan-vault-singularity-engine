# Titan Vault

This vault holds a project's **prescriptive model**: the schema that defines a model document, and the versioned model content itself. The model is authored here first; the code is brought into conformance with it.

**New here? Read [`workflow.md`](workflow.md)** — how to author a model version.

## Scope — what this models

This vault models a **domain**: what the software must be true *about* — the entities, meaning, and constraints of the subject matter it manages and enforces. It does **not** model the software's implementation (its classes, services, architecture).

The implementation *realizes* this model: the **code** is brought into conformance with the **formal** directly — never with the prose, which is only a human rendering. A *model of the software* (UML / C4) is a separate concern, not part of this vault.

*(If the domain is itself about modeling — a tool for building models — the content will read as a vocabulary of modeling concepts. That is still the domain, not the software.)*

## Contents

- **`workflow.md`** — how to author and evolve a model version (start here).
- **`schema.formal.md`** — the formal specification of a model document: its **grammar** (the admissible syntax) and its **constraints** (the well-formedness rules).
- **`schema.prose.md`** — the plain-language companion to the schema, with the versioning and formal/prose conventions.
- **`reference/`** — the DECLARE template catalogue: a lookup menu for the dynamics layer.
- **`model/`** — the model itself: one directory per version, each holding `formal/` and `prose/`.
