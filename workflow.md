# Workflow

How to author a model **version** with this vault. (This is the *usage* workflow. How the vault's *schema* was itself designed is a separate, one-time concern, not described here.)

## The loop

The agent works in the canonical tier; the human reviews the readable one.

1. **Author the formal** — write the `*.formal.md`, conforming to `schema.formal.md`.
2. **Derive the prose** — write the `*.prose.md` as its faithful mirror (see §3).
3. **Human reviews the prose** — the prose is the review surface. If it's wrong, fix the **formal** (it stays canonical) and re-derive the prose.

The formal is always the source of truth; the prose is always derived from it.

## 1. Starting a version

Versions live in `model/`, one directory per version, named `v<N>` (`v1`, `v2`, …).

- **First version:** create `model/v1/formal/` and `model/v1/prose/`, and author fresh.
- **Later versions:** `cp -r model/v<N> model/v<N+1>`, then edit. Each version is a **full, self-contained snapshot** — it never refers out to another version.

The examples in `schema.*.md` are *illustrations*, not a template to copy.

## 2. Authoring order

Author the three layers in **causal order** — each builds on the vocabulary of the one before:

> **structure → invariants → dynamics**

Within each layer, **formal before prose**.

## 3. The prose mirror

Each `*.prose.md` is derived from its `*.formal.md` and is the human's review surface. It must satisfy the **prose-mirror contract in `schema.prose.md` §3** (the single source of truth) — in short: headings 1:1, one entry per formal id, nothing added or dropped, each entry a short bullet (id + plain-language statement + formal form in backticks).

After **any** formal edit, re-derive and re-check the prose. Drift between the tiers is a defect, not a variant.

## 4. DECLARE templates (dynamics)

For the dynamics layer, copy the **used subset** of `reference/declare-templates.md` into the version's `## Templates` section, and coin custom templates the same way. Copy only what's used; the snapshot stays self-contained. When a copied template is defined via others (e.g. `Succession ≝ Response ∧ Precedence`), copy those dependencies too — or inline them — so every applied name resolves (`W9`). Copy-vs-inline is an author's choice; it changes the prose surface (one bullet per copied template) but not the meaning, so reviewers shouldn't read the difference as drift.

## 5. Status

A version carries a `STATUS` file in its directory — kept out of the schema-governed documents, which begin at their `# Heading` — holding exactly one of these tokens, lowercase, on a single line:

- `draft` — being authored;
- `ready-for-review` — the authoring agent has finished and self-verified (§6); awaiting human review;
- `final` — a human has reviewed and accepted it.

## 6. Definition of done

Done is split between the agent and the human.

**The authoring agent's part** (→ `ready-for-review`) — fully self-checkable:

- all six files exist — `formal/` + `prose/` × {structure, invariants, dynamics};
- each formal document **parses and resolves** against `schema.formal.md` — the grammar is signature-directed (`schema.prose.md` §2.4), so this is a context-free parse *plus* name/arity/cardinality resolution, not a parse alone;
- all **well-formedness constraints `W1–W10`** hold (verify each against `schema.formal.md`);
- each prose document **mirrors** its formal counterpart 1:1 (§3);
- the model is **faithful to the requirements**.

**The human's part** (→ `final`): review the prose and accept it — or return corrections, which fix the *formal* (canonical) and re-derive the prose.

## 7. Committing

Commit a version as **one atomic commit** once it is `final` (after human acceptance) — message `model: add v<N> — <one-line summary>`. One commit per version.
