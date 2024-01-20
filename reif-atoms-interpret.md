# Proposal - atomic reification - interpretation version

## Abstract Syntax
```
    graph            ::= (triple)* 
    triple           ::= subject predicate object 
    subject          ::= iri | BlankNode | descriptor 
    predicate        ::= iri 
    object           ::= term 
    term             ::= iri | BlankNode | literal | descriptor 
    descriptor       ::= descriptor(subject predicate object)
```

A _term_ is denoted by `r`, a _triple_ by `t`, and a _graph_ by `g`.  

Given a _triple_ `t`, we denote the subject, predicate, object of `t` as `t.s`, `t.p`, `t.o`, respectively.  

Given a _descriptor_ `r`, we denote the subject, predicate, object positions of `r` as `r.s`, `r.p`, `r.o`, respectively.  

RDF 1.1 syntax is the above without the _descriptor_ category.

## Reification resource

A "reification atom" (type?) is a resource in IR.

It is a relationship of arity 3, `reif(s, p, o)`.

Other posisble names include "occurrence", "occurrence type". (then "descriptor" can be "reif").

"Naming", as in "named occurrence", token, is a resource denoted by the subject of triple "N rdf:occurrenceOf Descriptor".

This is the parallel to named reification-as-token.

### Semantics

An RDF-star simple interpretation `I` is a structure:
<`IR`, `IP`, `IA`, `IS`, `IL`, `ID`, `IEXT`>
consisting of:

1. A non-empty set `IR` of _resources_, called the domain or universe of `I`.
2. A set `IP`, called the set of _properties_ of `I`.
3. A set `IA`,  called the set of _reification atoms_ of `I`.
4. A mapping `IS` from IRIs into `IR ⋃ IP ⋃ IA`, called the _interpretation_ of IRIs.
5. A partial mapping `IL` from _literal_ into `IR`, called the _interpretation_ of literals.
6. A mapping `ID` from descriptors into `IR` called the _interpretation_ of descriptors.
7. A mapping `IEXT` from `IP` into `2<sup>IR x IR</sup>`, called the _extension_ of properties.

`A` is a mapping from `BlankNode` to `IR`.

Given `I` and `A`, the function `[I+A](.)` is defined over _terms_, _triples_, and _graphs_ as follows.

#### Terms

- `[I+A](r) = IS(r)`    if `r` is a _iri_
- `[I+A](r) = IL(r)`    if `r` is a _literal_
- `[I+A](r) = A(r)`     if `r` is a _BlankNode_
- `[I+A](r) = ID(r)`    if `r` is a _descriptor_

where
`ID: d -> reif([I+A](d.s), [I+A](d.p), [I+A](d.o))`

The components of a descriptor denote the same resources as when used in triples.
"reification atom" however are not "TRUE" (asserted).

<small><small><i>(
need help on this part to make sure it is correctly expressed:
We have defined the resource in IR for an atomic reification.
A 'descriptor' (an element of the abstract syntax) maps to a 'reif', an element of the universe of I, by applying [I+A] to the components and translating the tuple name.
)</i></small></small>

#### Triples

- `[I+A](t) = TRUE` if and only if `<[I+A](t.s),[I+A](t.o)> ∈ IEXT([I+A](t.p))`

#### Graphs

- `[I+A](g) = TRUE` if and only if `∀ t ∈ g . [I+A](t) = TRUE`

#### Models

An interpretation `I` is called a _model_ of a graph `g` if there exists `A` such that
`[I+A](g) = TRUE`.

The set of all models of a graph `g` is called `models(g)`.

Simple entailment: `g ⊨ g'` if and only if `models(g) ⊆ models(g')`.
