# Schema

## Grammar

```
Document      ::= "# Structure" Concepts Taxonomy Properties Relations

Concepts      ::= "## Concepts" ConceptDecl+
ConceptDecl   ::= EntityLine | EnumLine
EntityLine    ::= Concept ("," Concept)*
EnumLine      ::= Concept "≡" "{" Value ("," Value)* "}"

Taxonomy      ::= "## Taxonomy" Subsumption*
Subsumption   ::= Concept "⊑" Concept

Properties    ::= "## Properties" PropertyAxiom+
PropertyAxiom ::= Concept "⊑" Restriction ("⊓" Restriction)*
Restriction   ::= Number Field "." Type

Relations     ::= "## Relations" RelationDecl+
RelationDecl  ::= Role ":" Concept Mult "→" Mult Concept
Mult          ::= "[" ("1" | "0..1" | "1..N" | "0..N") "]"

Number        ::= ("=" | "≤" | "≥") Nat
Type          ::= Primitive | Concept
Primitive     ::= "String" | "Integer" | "Boolean" | "ID"
Concept       ::= Name
Name | Field | Role | Value ::= Ident
Ident         ::= Letter (Letter | Digit)*
Nat           ::= Digit+
```

## Constraints

With **D** = the concepts declared in `## Concepts` (entities ∪ enumerated value types) and **P** = the primitives `{String, Integer, Boolean, ID}`:

- **W1 — Declared.** Every concept named in Taxonomy, Properties, or Relations is in **D**:  `∀c. mentioned(c) → c ∈ D`.
- **W2 — Typed.** Every type in a Properties restriction is primitive or declared:  `∀t. type(t) → t ∈ P ∪ D`.
- **W3 — Hierarchy.** Subsumption is irreflexive and acyclic:  `∀c. ¬(c ⊑ c)`  and  `⊑⁺` has no cycle.
- **W4 — Unique.** Each concept is declared once; each field is unique within its concept; each role is declared once.
