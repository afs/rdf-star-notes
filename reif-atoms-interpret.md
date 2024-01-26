# Proposal - atomic reification - interpretation

The presentation here is taken from [an earlier semantics](https://github.com/w3c/rdf-star-wg/wiki/Semantics:-Andy's-proposal).

An alternative approach to the semantics is "[A proposal for RDF-star semantics](https://www.emse.fr/~zimmermann/W3C/RDF-star-semantics/)".

## Abstract Syntax

```
    graph            ::= (triple)* 
    triple           ::= subject predicate object 
    subject          ::= iri | BlankNode
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

A "reification atom" is a resource in IR.  We write `reif(s,p,o)` for these resources 
and write as `x.s`, `x.p`, `x.o` for the three components of a reification atom `x`.

Descriptors are mapped by the interpretation into this special kind of resource.

(Other possible names include "occurrence type" and others)

"Naming", as in a "named occurrence", a token, is a resource. It will need to have a relation with a reification atom to indicate what is it is a "named occurrece of". For example: "N rdf:occurrenceOf Descriptor".

This is the parallel to named reification-as-token.

### Semantics

An RDF-star simple interpretation `I` is a structure:
<`IR`, `IP`, `IA`, `IS`, `IL`, `ID`, `IEXT`>
consisting of:

1. A non-empty set `IR` of _resources_, called the domain or universe of `I`.
2. A set `IP`, called the set of _properties_ of `I`.
3. A set `IA`, called the set of _reification atoms_ of `I`.
4. A mapping `IS` from IRIs into `IR ⋃ IP`, called the _interpretation_ of IRIs.
5. A partial mapping `IL` from _literal_ into `IR`, called the _interpretation_ of literals.
6. A mapping `ID` from descriptors into `IA` called the _interpretation_ of descriptors.
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
A "reification atom" however are not "TRUE" (asserted).

<small><small><i>(
need help on this part to make sure it is correctly expressed.
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
