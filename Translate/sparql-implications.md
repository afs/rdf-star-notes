# SPARQL 

**WIP**

This note covers the effects of translation-based approaches to RDF-star.

Translation-based approaches do not change the semnatics of RDF. They translater either concrete syntax to triples ([\[1\]](https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0095.html) or [\[2\]](https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0104.html)) or apply a conversion to the abstract syntax [\[3\]](./reif-atoms-translate.md).

SPARQL is based on simple entailment matching for BGPs, the an algrebra working on the abstract synatx. It is possible to think of entailment matching as matching patterns on triples.

SPOARQL is not working with the data concrete syntax and even if the [Turtle-agreed syntax](https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0095.html) is ported to SPARQL, the

## Case 2 : Counting

## Case 1 : Counting

For data assert-occurrence-annotate:
```
   :s :p :o {| :a :b |}
```
```
   :s :p :o .
   _:b rdf:subject :s .
   _:b rdf:predicate :p .
   _:b rdf:object :o .
   _:b :a :b .
```
the query
```
   SELECT (count(*) AS ?c) { ?s ?p ?o }
```
returns `?c = 5` (or 6 if there is an rdf:type rdf:Statement triple).

## Case 1 : Functions

SPARQL functions are pure.

(there are some "functional forms" that are not strict functions. e.g `&&`, `COALESCE` which access unevaluated arguments)

For data:
```
   _:b rdf:occurrenceOf <<( :s :p :o )>> .
```
the query
```
   SELECT ?N { ?N rdf:occurrenceOf <<( :s :p :o )>> }
```
gives all names for a specific reification atom.
```
   SELECT DISTINCT ?N { ?N rdf:occurrenceOf <<( :s ?p ?o )>> }
```
is all names for reification atoms that use subject `:s`.

## Case 2 : Counting

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

### Other

As well as the Turtle syntax+variables pattern matching, SPARQL would also have accessor functions
[SUBJECT](https://w3c.github.io/rdf-star/cg-spec#subject),
[PREDICATE](https://w3c.github.io/rdf-star/cg-spec#predicate),
[OBJECT](https://w3c.github.io/rdf-star/cg-spec#object)
and parallels to other forms in the [CG report](https://w3c.github.io/rdf-star/cg-spec#builtin-functions).

