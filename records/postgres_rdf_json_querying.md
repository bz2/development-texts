# RDF in PostgresSQL

The Shared Meaning Platform currently stores some RDF intermediate data in
PostgresSQL. We should review the specifics of how this works to ensure it
makes some kind of sense.


## History

The initial proof of concept used postgres to store all data, including RDF
data in a table with one row per triple, for simplicity.

To reduce the number of rows, and for the moment match the exact format used
by the api, each graph is now a JSON field with an array of all terms, in the
format:

```json
[
      {"subj": aSubject, "pred": aPredicate, "obj": anObj},
      ...
]
```

All values are strings, with no additional type markers or signifiers. This
means IRIs and literals are not distinguished.

This is not a standard RDF serialisation format, and does not expose any
structure that would be useful for queries.


## Why not a graph database?

Graph databases aim to do two things well:

* Store millions of terms
* Resolve complex queries

We don't really have either problem yet. Our graphs are split by project and
will remain quite small, and querying makes most sense to do client-side for
the immediate future.


## Considerations

The most common operation at present is loading the whole JSON document and
sending it across the network. The only write operation replaces the entire
graph. Multiple graphs are stored as separate documents.

[PostgreSQL JSON Types] describes shapes of JSON which enable pattern based
querying which may become useful for some operations.

PostgresSQL offers `json` and `jsonb` type with slightly different
characteristics. We currently use `jsonb` as the exact input text is
unimportant for RDF.

The main JSON serialisation for RDF is [JSON-LD] which at the complex end of
the scale, with local context binding, multiple representations for the same
information, and extends the RDF data model.

[N3.js Parsing] supports multiple formats, but incremental loading isn't
particularly important for JSON (it's hard to do, and browser engines already
make it fast). The internal store uses objects keyed on subject and predicate
for better querying.


## Question

Would storing the JSON in a different shape make more sense right now?

It's easy enough to migrate the existing data, and make the client understand
a new arrangement.

Given we're uninterested in blank nodes, an object keyed by subject IRI perhaps
makes sense.

Alternatively maybe this JSON business is a bad idea and a canonical form of
[N-Triples] is just better.


## Future

At some point the right way to manage this content should be through git, per
[Distributed Collaboration on RDF Datasets Using Git], though keeping `HEAD`
in the database for some client operations will likely continue to make sense.


[PostgreSQL JSON Types]: https://www.postgresql.org/docs/current/datatype-json.html
[N-Triples]: https://www.w3.org/TR/n-triples/
[N3.js Parsing]: https://github.com/rdfjs/N3.js#parsing
[JSON-LD]: https://w3c.github.io/json-ld-syntax/
[Distributed Collaboration on RDF Datasets Using Git]: https://ul.qucosa.de/api/qucosa%3A15781/attachment/ATT-0/
