# Proposal - atomic reification

This is a reification-based semantics proposal.

The problems with RDF 1.1 reification include:

1. Visually verbose (Turtle).
2. Verbose number of triples (N-Triples).
3. The reification triples may be spread across parts of a document - breaks visual proximity as written.
4. Over-specificed reifications (e.g. multiple rdf:subject)
5. Under-specificed reifications (e.g. no rdf:subject)
6. Presence or absence of rdf:Statement is unclear leading to different SPARQL results.  
   (De facto, it is omitted.)
7. If a large graph is split into multiple files of manageable size, the triples can be split across those files (and blank nodes for reification are broken).

The agreed Turtle syntax hides some of these issues (addresses 1, tends away from 3, 4 and 5) but not for N-triples, and not for SPARQL results when thought of as querying a triple table.

## General idea

Introduce a unit "reification atom" that means the equivalents of rdf:subject/rdf:predicate/rdf:object are not split.

There is a syntax "descriptor"; the name is choosen _pro tem_ so that it does not presume too much.

It is distinct from 'triple' in the abstract syntax.

Whether this "is" reification written differently can be decided later when the details are stable. 

This is not the first such composite RDF term in RDF. A literal has a lexical form and a datatype, as well as optional language and initial text direction.

N-Triples syntax, updated the RDF data model, and semantics included - as has been requested.

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

Given a descriptor `x`, we denote the subject, predicate, object positions of `x` as `x.s`, `x.p`, `x.o`, respectively.  

A _named occurrence_ is a resource associated with a "reification atom" via a triple.
A named occurrence is a token of the reification atom (type).

"named triple" is another possible name which may be more accessible to the general reader of RDF specs.

```
    N rdf:occurrenceOf descriptor .
```
where descriptor is the syntax element (RDF term) for a reification atom.

Other names are possible. This is using one name that has been in circulation in the WG.

## The idea for semantics.

Descriptors are mapped by the interpetation to a resource, a "reification atom",
that is an element of IR. 

It is "along side" the reification vocabulary. It provides the same capability, a named token for a triple, but in an atomic form.

Whether "reification atom" is "rdf:Statement", and the reification properties apply, is left open.

[Interpret Reification Atoms](./reif-atoms-interpret.md)

## Concrete Syntax Implications

### Turtle

[As agreed](https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0095.html)

### N-Triples

A "descriptor" is syntactic representation of a reification atom `<<(s p o)>>`. It is a token/terminal in the grammar. It can only appear in the object position.

Descriptors can be written in Turtle but agreed syntax makes that a burdensome way to write RDF.

The triples `M rdf:occurrenceOf AR` correspond to the "edge set" and can be implemented that way, or as secondary indexes, for better handling of searching AR.

Turtle:
```
   << :e | :s :p :o >> :a :b .
```
 N-triples:
```
    :e rdf:occurrenceOf <<( :s :p :o )>> .
    :e :a :b .
```

## SPARQL Implications

SPARQL is based on simple entailment matching for BGPs, then an algrebra working on the abstract syntax elements. It is possible to think of simple entailment matching as matching patterns on triples.

For data:
```
   _:b rdf:occurrenceOf <<( :s :p :o )>> .
```
the query
```
   SELECT (count(*) AS ?c) { ?s ?p ?o }
```
returns `?c = 1`.

For data assert-occurrence-annotate:
```
   :s :p :o {| :a :b |}
```
```
   :s :p :o .
   _:b rdf:occurrenceOf <<( :s :p :o )>> .
   _:b :a :b .
```
the query
```
   SELECT (count(*) AS ?c) { ?s ?p ?o }
```
returns `?c = 3`.

"Reification" cost is one triple.

```
   SELECT ?N { ?N rdf:occurrenceOf <<( :s :p :o )>> }
```
is all names for an occurence.

```
   SELECT DISTINCT ?N { ?N rdf:occurrenceOf <<( :s ?p ?o )>> }
```
is all names for reification atoms that use subject `:s`.

Get the resource for, and subject of, named occurences:
```
   SELECT ?N (SUBJECT(?x) AS ?subj ){ 
      ?N rdf:occurrenceOf ?x .
   }
```

As well as the Turtle syntax+variables pattern matching, SPARQL would also have accessor functions
[SUBJECT](https://w3c.github.io/rdf-star/cg-spec#subject),
[PREDICATE](https://w3c.github.io/rdf-star/cg-spec#predicate),
[OBJECT](https://w3c.github.io/rdf-star/cg-spec#object)
and parallels to other forms in the [CG report](https://w3c.github.io/rdf-star/cg-spec#builtin-functions).
