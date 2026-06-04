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

Each `*.prose.md` is derived from its `*.formal.md` and is the human's review surface. It must:

- mirror the formal's **headings** one-to-one;
- carry **one entry per formal id** — each concept, field, relation, `I#`, `D#` — under the same id;
- **add and drop nothing**: every formal statement appears, no constraint is invented;
- render relation multiplicities per `schema.prose.md` §2.1.4, and rules with their template label plus a plain-language gloss.

After **any** formal edit, re-derive and re-check the prose. Drift between the tiers is a defect, not a variant.

## 4. DECLARE templates (dynamics)

For the dynamics layer, copy the **used subset** of `reference/declare-templates.md` into the version's `## Templates` section, and coin custom templates the same way. Copy only what's used; the snapshot stays self-contained.

## 5. Marking work-in-progress

While a version is being authored, mark it `status: draft` (a line at the top of each file, or a `STATUS` file in the version directory). Remove it (or set `status: final`) when done. **Absence of a marker means final.**

## 6. Definition of done

A version is done when:

- all six files exist — `formal/` + `prose/` × {structure, invariants, dynamics};
- each formal document **parses** against `schema.formal.md`;
- all **well-formedness constraints `W1–W10`** hold (verify each against `schema.formal.md`);
- each prose document **mirrors** its formal counterpart 1:1 (§3);
- the model is **faithful to the requirements**;
- the **human has reviewed and accepted the prose**.

## 7. Committing

Once done, commit the version as **one atomic commit**, message `model: add v<N> — <one-line summary>`. One commit per version.
