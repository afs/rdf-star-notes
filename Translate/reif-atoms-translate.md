# Proposal - atomic reification - translation version

### Abstract Syntax
```
    graph            ::= (triple)* 
    triple           ::= subject predicate object 
    subject          ::= iri | BlankNode
    predicate        ::= iri 
    object           ::= term 
    term             ::= iri | BlankNode | literal | descriptor 
    descriptor       ::= descriptor(s p o)
```

RDF 1.1 syntax is the above without the _descriptor_ category.

### Translation

For interpretation, for each triple RT of the form `X rdf:occurrenceOf D` (where X is a URI or blank node, and D is a descriptor) add triples:

```
   X rdf:subject D.s .
   X rdf:predicate D.p .
   X rdf:object D.o .
```

### SPARQL

SPARQL works on the abstract syntax but uses simple entailment for BGPs. Matchign BGPs is an extension point in SPARQL.

In SPARQL 1.1, a developer can think of BGP matching as accessing N-triples. Now, the differences between thinking of BGP matching as matching N-triples and simple entailment are visible.

[18.3.1 SPARQL Basic Graph Pattern Matching](https://www.w3.org/TR/sparql12-query/#BGPsparql).

For data:
```
   _:b rdf:occurrenceOf <<( :s :p :o )>> .
```
the query
```
   SELECT (count(*) AS ?c) { ?s ?p ?o }
```
returns `?c = 1` as N-Triples and `?c = 4` for directly using simple entailment.

```
_:b rdf:occurrenceOf <<( :s :p :o )>> .
_:b rdf:subject :s .
_:b rdf:predicate :p .
_:b rdf:object :o .
```

Any query that has a predicate variable has this potential because e.g. `rdf:subject` is not in the N-Triples.
