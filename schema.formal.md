# Schema

## structure.formal.md

### Grammar

```
StructureDoc  ::= "# Structure" Concepts Taxonomy Properties Relations

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
```

`Ident`, `Nat` as in `## Common`.

### Constraints

With **D** = the concepts declared in `## Concepts` (entities ∪ enumerated value types), **E** ⊆ **D** the enumerated value types (each defined by an `EnumLine`), and **P** = the primitives `{String, Integer, Boolean, ID}`:

- **W1 — Declared.** Every concept named in Taxonomy, Properties, or Relations is in **D**:  `∀c. mentioned(c) → c ∈ D`.
- **W2 — Typed.** Every type in a Properties restriction is a primitive or an enumerated value type — never an entity:  `∀t. type(t) → t ∈ P ∪ E`.
- **W3 — Hierarchy.** Subsumption is irreflexive and acyclic:  `∀c. ¬(c ⊑ c)`  and  `⊑⁺` has no cycle.
- **W4 — Unique.** Each concept is declared once; each field is unique within its concept; each role is declared once.

## invariants.formal.md

### Grammar

```
InvariantsDoc ::= "# Invariants" Invariant+
Invariant     ::= "I" Nat Formula
Formula       ::= Atom | "¬" Formula | Formula Connective Formula | Quantifier VarList "." Formula | "(" Formula ")"
```

`Atom`, `Connective`, `Quantifier`, `VarList`, `Nat` as in `## Common`.

### Constraints

- **W5 — Unique ids.** Invariant ids (`I1`, `I2`, …) are distinct.
- **W6 — Grounded.** Every predicate in an invariant resolves to a declared concept (arity 1) or relation (arity 2); every function to a declared **required** (`=1`) property (arity 1) — an optional or multi-valued property appears instead as a binary predicate. Arities match the structure. A bare `Value` term is a member of a declared enumerated value type. Order comparisons (`<`, `≤`, `>`, `≥`) relate only `Integer`-typed terms (a numeral, or an `Integer`-valued property function).
- **W7 — Closed.** Every invariant is a closed formula — no free variables.

## dynamics.formal.md

### Grammar

```
DynamicsDoc  ::= "# Dynamics" Events Templates Rules

Events       ::= "## Events" EventDef*
EventDef     ::= Name "(" VarList ")" "≝" Formula

Templates    ::= "## Templates" TemplateDef*
TemplateDef  ::= Name "(" ParamList ")" "≝" Formula

Rules        ::= "## Rules" Rule+
Rule         ::= "D" Nat Formula

Formula      ::= Atom
               | Name "(" ArgList ")"            -- use of an event or template
               | Param
               | "¬" Formula
               | ("G" | "F" | "X") Formula
               | Formula "U" Formula
               | Formula Connective Formula
               | Quantifier VarList "." Formula
               | "(" Formula ")"
Arg          ::= Term | Formula
ArgList      ::= Arg ("," Arg)*
ParamList    ::= Param ("," Param)*
Name | Param ::= Ident
```

`Atom`, `Term`, `Connective`, `Quantifier`, `VarList`, `Nat`, `Ident` as in `## Common`. An `EventDef`'s parameters are term variables; a `TemplateDef`'s are formula parameters.

### Constraints

- **W8 — Acyclic.** The `≝` definitions (events and templates) are acyclic — no definition's body refers, directly or transitively, to the name it defines — so every defined name expands to raw LTLf over the structure's vocabulary.
- **W9 — Grounded.** Every atom resolves to the structure (predicate = declared concept of arity 1 or relation of arity 2; function = declared **required** (`=1`) property). Every applied name resolves to a definition of matching arity — an **event** (declared in `## Events`) is applied to terms; a **template** (declared in `## Templates`) is applied to formulas. A bare `Value` term is a member of a declared enumerated value type. Order comparisons (`<`, `≤`, `>`, `≥`) relate only `Integer`-typed terms.
- **W10 — Closed.** Every rule (`D1`, `D2`, …) is a closed formula — no free variables.

## Common

The first-order core and lexis shared by the grammars above. (`Concept`, `Role`, `Field`, `Value` are the signature names a `structure.formal.md` declares; a `Value` is a member of an enumerated value type.)

```
Atom        ::= Pred "(" Term ("," Term)* ")" | Term Compare Term
Term        ::= Var | Value | Numeral | Func "(" Term ")"
Compare     ::= "=" | "<" | "≤" | ">" | "≥"
Numeral     ::= "-"? Nat
Pred        ::= Concept | Role | Field
Func        ::= Field
Quantifier  ::= "∀" | "∃"
Connective  ::= "∧" | "∨" | "→" | "↔"
VarList     ::= Var ("," Var)*
Var         ::= Ident
Ident       ::= Letter (Letter | Digit)*
Nat         ::= Digit+
```
