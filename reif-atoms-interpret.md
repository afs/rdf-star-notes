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

Given a _descriptor_ `r`, we denote the subject, predicate, object positions of `r` as `r.s`, `r.p`, `r.o`, respectively.  

Given a _triple_ `t`, we denote the subject, predicate, object of `t` as `t.s`, `t.p`, `t.o`, respectively.  

RDF 1.1 syntax is the above without the _descriptor_ category.

## Reification resource

A "refication atom" is a resource into IR.

It is a relationship of arity 3, reif(s, p, o).  

Other names "occurrence", ...

"Naming", as in "named occurrence", is via a triple "N rdf:occurrenceOf Descriptor".
This is the parallel to named reification-as-token.

### Semantics

An RDF-star simple interpretation `I` is a structure:
<`IR`, `IP`, `IA`, `IS`, `IL`, `IT`, `IEXT`, `ID`>
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
- `[I+A](r) = ID(reif([I+A](r.s), [I+A](r.p), [I+A](r.o)))` if `r` is a descriptor
- `[I+A](r) = A(r)`     if `r` is a _BlankNode_

the elements of a descriptor denote the same resources as when used in triples.
"descriptors" however are not "TRUE" (asserted).

#### Triples

- `[I+A](t) = TRUE` if and only if `<[I+A](t.s),[I+A](t.o)> ∈ IEXT([I+A](t.p))`

#### Graphs

- `[I+A](g) = TRUE` if and only if `∀ t ∈ g . [I+A](t) = TRUE`

#### Models

An interpretation `I` is called a _model_ of a graph `g` if there exists `A` such that
`[I+A](g) = TRUE`.

The set of all models of a graph `g` is called `models(g)`.

Simple entailment: `g ⊨ g'` if and only if `models(g) ⊆ models(g')`.
