## Objective 

Direct users through an SMP-native painpoint/stakeholder annotation process that allows them to respond to map content. 

The raw response data for each annotation is stored in triples like this:

| Subject | Predicate | Object |
|---------|-----------|--------|
| annotation IRI | creator | joe.bloggs@example.com |
| annotation IRI | hasTarget | stakeholder/painpoint IRI |
| annotation IRI | hasBody | "Some comment text" |
| annotation IRI | hasCategory | "Surprising" |

This can be easily aggregated into an output table with the following structure, accessed via API:

| Participant | Object | Category | Comment |
|-------------|--------|----------|---------|
| joe.bloggs@example.com | Example Painpoint | Surprising | null |
| joe.bloggs@example.com | Example Stakeholder | Question | "blahblahblah" |
...

This may then be further aggregated as desired -- for example, into an equivalent data format as the table in the existing Powerpoint doc.

## Process

### Defining Categories

Annotation categories can be defined per map from the map data sheets.
Example below assumes a new "Annotation Categories" sheet is added with the following structure:

| Name | Icon Url | Selected Icon Url | Help Text | Uses Mask | Description |
|-------------|--------|----------|---------|---------|---------|
| Interesting | //host.com/icon.svg | //host.com/inverse.svg | I find this interesting because... | true | Interesting category description |
| Question | //host.com/icon.svg | //host.com/inverse.svg | I have a question... | 1 | Question category description |

This is then transformed into RDF through an annotation categories transform.

```
[
    {
        'sheet': 'Annotation Categories',
        'lets': {
            'iri': 'vm:AnnotationCategory/{row[Name].as_slug}',
        },
        'triples': [
            ('{iri}', 'rdf:type', 'vm:AnnotationCategory'),
            ('{iri}', 'vm:name', '{row[Name].as_text}'),
            ('{iri}', 'vm:hasIcon', '{row[Icon Url].as_text}'),
            ('{iri}', 'vm:hasAltIcon', '{row[Selected Icon Url].as_text}'),
            ('{iri}', 'vm:useMask', '{row[Uses Mask].as_json}'),
            ('{iri}', 'vm:display', '{row[Help Text].as_text}'),
            ('{iri}', 'vm:description', '{row[Description].as_text}'),
        ],
    }
]
```

E.g. output in Turtle format:
```
<http://visual-meaning.com/rdf/AnnotationCategory/interesting> a vm:AnnotationCategory ;
    vm:description "Interesting category description" ;
    vm:display "I find this interesting because…" ;
    vm:hasAltIcon "//host.com/icon-inverse.svg" ;
    vm:hasIcon "//host.com/icon.svg" ;
    vm:name "Interesting" ;
    vm:useMask "true" .
```

`Uses Mask` signifies whether icons should be used as a CSS masks, or as simple background images.
CSS masks allow the icons to have branded colors, but come with the drawback that only the alpha values are considered when applying. This essentialy makes masked icons monochrome - opaque areas have the brand primary color applied, while fully transparent ones end up being white. Everything in between is a mix of primary and white.

#### Maps with no categories

Maps which do not need different annotation categories can still enable commenting by defining a single row in the "Annotation Categories" sheet.
Example below uses the default comment icons.

| Name | Icon Url | Selected Icon Url | Help Text | Uses Mask | Description |
|-------------|--------|----------|---------|---------|---------|
| Comment | //opatlas-live.s3.amazonaws.com/icons/Comment-Mask.svg | //opatlas-live.s3.amazonaws.com/icons/Comment-Inverse-Mask.svg |  | true | The default comment category |

### User identity

A user is authorised to access a map by including their email address (or a wildcarded version of it) in the whitelist for the map's project.

The user is emailed a link to the map. When they access it, they are first prompted to enter their email email. This is checked against the whitelist for access, and will also be used as their identity for entering annotations.

### Explaining the exercise to user

When the user accesses the map for the first time, they are greeted with an Introduction splash outlining the exercise and explaining how to leave annotations (categories and comments) on painpoints/stakeholders.

### Leaving annotations

Clicking on a painpoint brings up the annotation interface. A user can assign a category to a painpoint (Interesting/Surprising etc.) and optionally include a comment. (Emptystring if not entered?)

The painpoint annotation data goes into the UserMapAnnotationBag (a database table) for that user. Each UserMapAnnotationBag is associated with a single user and a single map.

### Output data

An API is called which aggregates all of the UserMapAnnotationBags for the target map together, and returns a tabular data file similar to the one outlined in the objective. 

## Questions:

Q: The MVP is an API that returns a dataset similar to that contained within the existing Powerpoint doc. Is this sufficient? 

A: *Yes, as MVP*.

Q: Users can currently make unlimited annotations. Is it necessary to limit the number of annotations which can be made so that feedback can be focused/prioritised? 

A: *No, it's fine for them to make as many annotations as they want*.

Q: Is being able to assign just a category to a painpoint (without associated comment) a necessary piece of functionality for this exercise? 

A: *Ideally the feedback process is as streamlined as possible (emoji-style), so the ability to leave it via a two- or three-click process of selecting a stakeholder/painpoint and then selecting a category without having to enter a full comment would be very nice.*


