# The Baumkataster dataset

The [Baumkataster dataset]() consists of the following data fields, which have been illustrated in the next table with some example rows:

| baumnummer  | kronendurchmesser | stammdurchmesser | art | baumart | gruenanlage | standortfkt | thegeom |
|---|---|---|---|---|---|---|---|
| GZAW870 |  12.0 |  48.0 | Weide | Salix spec.  |  Grünzug Altenwerder | sonst. öff. Flächen  |  POINT(9.918514827294882, 53.498226989745973) |
| GZAW883  | 6.0 | 19.0 | Spitz-Ahorn  | Acer platanoides  | Grünzug Altenwerder  |  sonst. öff. Flächen | POINT(9.918501999352625, 53.497818014776414) |
| MOBG657 |  5.0 | 32.0  |  Vogel-Kirsche | Prunus avium | Moorburg | sonst. öff. Flächen | POINT(9.91853784213246, 53.495375315136243)  |

We can see that this dataset is in an open format (GeoJSON) and meets the 3-star criteria for linked open data. 
However, it lacks representation in RDF and links to other data resources to be a 5-star LOD.

To convert this dataset, we first need to gather some initial information about it:

A first analysis of the dataset should answer some of the following questions:

- Which datatype can we expect per column of this dataset?
  - Number: Integer e.g. 2, Double e.g. 4.0
  - Boolean: true/false, ja/nein, yes/no, on/off
  - String: Unique String, Categorized String?
 
This analysis can be done with a simple script that checks for uniqueness and data types. Even software like QGIS supports simple column detection.  

## Examination of data fields

Let's investigate the data fields and any other information we need for conversion in this section.

### Classification of the dataset

Here, we ask which semantic class to assign to the dataset.
We follow the following guidelines:

- Which Semantic class, that is sufficiently precise, describes all instances of the dataset?

For our Baumkataster example, a good classification might be sth. like **tree**.

Bad classifications include:

- **Plant**: Not specific enough
- **Oaks**: Does not cover the whole dataset
- **HamburgTrees**: Unnecessary high specificity and unnecessary mix of two different concepts in one class

#### How to create a classification **tree**?

The Semantic Web encourages us to reuse already existing definitions for **tree** if:

- They exist elsewhere in the Semantic Web already
- The definition matches our understanding of **tree** which is valid for this dataset

**Defining a tree in the context of this dataset?**

- A wooden plant with leaves? (Bushes have leaves and wood....)
- A plant with a trunk and crown?

Defining sth. in a domain of which you are not an expert is not easy.

How about reusing a definition of Wikidata? [tree (Q10884)](https://www.wikidata.org/wiki/Q10884)

> EN: perennial woody plant

> DE: eine verholzte Pflanze, die aus einer Wurzel, einem Stamm und einer Krone besteht

> [!NOTE]
> **CHOICE:** Reusing the Wikidata definition [tree (Q10884)](https://www.wikidata.org/wiki/Q10884) including its own definition. We add a German label "Baum"@de and an English label "tree"@en

### Identifying and labeling instances

Every instance in the dataset should be identifiable by its own URI once the RDF conversion is complete.
To ensure the uniqueness of data identification, we need to ensure that the dataset includes a local identifier.
To that end, the preferred way is to find an identifier in our dataset.
Another alternative would be to generate another column with an identifier.

In our dataset, we therefore look for a column in our dataset that:
- Has unique values
- Has an indication of being an identifier from the column description, sth. like "id", "number", a.s.o.
- Covers every instance of the dataset

| baumnummer | 
|---|
| GZAW870 |  
| GZAW883 |
| MOBG657 | 

In the Baumkataster case, the only column that fulfills all aforementioned criteria is "baumnummer", a unique number assigned to each tree.
We will keep this identifier in mind and apply it in a later step.

#### Instance labels

Local identifiers also make for good components of instance labels.
We might label a single tree merely "GZAW870", but maybe a better variant would be to name it "Baum {baumnummer}" in German and "tree {baumnummer}" in English.
We preserve the local identifier and add a more human-readable notion of a label at the same time

> [!NOTE]
> **CHOICE:** We treat baumnummer as the local identifier for this dataset. The identifier becomes part of the instance label in German and English.

**Resulting Mapping Definitions:**
```json
...
"id":"baumnummer,
...
"columns":{
    "baumnummer": {"propiri": "http://www.w3.org/2000/01/rdf-schema#label","prop": "anno","lang":"de", "prefix":"Baum "},
}
```

### Treating columns with numbers

The Baumkataster dataset contains two columns whose values are exclusively numbers: **kronendurchmesser** and **stammdurchmesser**.
Both columns include double numbers.


| kronendurchmesser | stammdurchmesser |
|---|---|
|  12.0 | 48.0 |
| 6.0 | 19.0 | 
|  5.0 | 32.0 |

Unfortunately, the dataset itself cannot tell us about the context of these number columns.

While the word "Durchmesser", diameter, would suggest a form of length measurement, we have no idea which measurement unit to apply.

This is where the mapping to linked open data needs to include information about the measurement unit, which may be derived from dataset documentation, the dataset provider, or plausibility checks by cross-referencing other datasets of similar kinds.

- How are tree trunk diameters usually measured? Does it make sense to have a tree trunk diameter unit of km?

Such treatments need to be applied to every column, including numeric ones, since each column could potentially describe sth in a given unit.

> [!NOTE]
> **CHOICE:** We treat kronendurchmesser and stammdurchmesser as DataTypeProperties with range [xsd:double](http://www.w3.org/2001/XMLSchema#double). kronendurchmesser will be assigned the unit [om:meter](http://www.ontology-of-units-of-measure.org/resource/om-2/meter) and stammdurchmesser the unit [om:centimetre](http://www.ontology-of-units-of-measure.org/resource/om-2/centimeter) .

```json
"kronendurchmesser": {"propiri": "http://www.wikidata.org/prop/direct/P2547",
"proplabels":{"de":"Kronendurchmesser","en":"crown diameter"},
"range":"http://www.w3.org/2001/XMLSchema#double",
"unit":"http://www.ontology-of-units-of-measure.org/resource/om-2/meter",
"prop": "data"
},
"stammdurchmesser": {
"proplabels":{"de":"Stammdurchmesser","en":"tree trunk diameter"},
"range":"http://www.w3.org/2001/XMLSchema#double",
"unit":"http://www.ontology-of-units-of-measure.org/resource/om-2/centimeter",
"prop": "data"
},
 
```

### Treating category String columns

Category string columns suggest some form of subcategorization.

Important questions to consider are:

- How does the subcategorization relate to the chosen dataset classification?
  Does the column actually subclassify a tree, or does it subclassify sth. else?
- Can we relate the subclassification to a URI schema from a data repository already existing in the Semantic Web?

In our dataset, we have two columns that fit this category: **art** and **baumart**.

**art** is a broader tree categorization, **baumart** is a more scientific tree categorization based on Latin designations.

The options here are as follows:
- Treat both columns as categorizations, i.e., a tree will be classified with a broader concept and a more specific concept
- Treat only the more specific concept as a subclassification and the other column as a categorization independent of the classification of the dataset

> [!NOTE]
> **CHOICE:** We use both columns for categorization.

To allow for categorizations, a mapping of String values to concept URIs needs to be created:
| baumart  | concept | label_en | label_de |
|---|---|---|---|
| Quercus robur |  [Q165145](https://www.wikidata.org/wiki/Q165145) |  Quercus robur | Stieleiche |
| Salix alba | [Q156918](https://www.wikidata.org/wiki/Q156918) | white willow | Silber-Weide |

### Treating unique String columns

None of the columns of the given dataset are unique String columns, so that a treatment of these columns does not apply here.

### Treating remaining String columns

Let's take a look at the remaining columns of this dataset.

| gruenanlage | standortfkt |
|---|---|
| Grünzug Altenwerder | sonst. öff. Flächen  |
| Grünzug Altenwerder  |  sonst. öff. Flächen |
| Moorburg | sonst. öff. Flächen   |

The two columns describe a park area and the status of the ground on which the tree has been planted, e.g., public-owned ground.

Similar to the String column category mentioned above, these terms could be mapped to concepts representing the respective entities described by the String values.
However, in practice, a categorization of ground areas did not seem readily available, and a brief search in Wikidata did not immediately yield clear concepts for the park areas either.
The following options remain here:
- Link OpenStreetMap concepts of the park areas (potentially unstable identifiers)
- Add definitions of the parks to a Linked Open Data resource (e.g., Wikidata or self-hosted)
- Treat the respective columns as comments. This would depend on an assessment of the need for integration

> [!NOTE]
> **CHOICE:** For the sake of simplicity in this example, we treat standortfkt as a comment.

**Resulting Mapping Definitions:**
```json
"standortfkt": {"prop": "data","propiri":"http://www.w3.org/2000/01/rdf-schema#comment","range":"http://www.w3.org/2001/XMLSchema#string","order": 1}
```

### The need for integration

Depending on the use case, not all information given inside the respective dataset is important for integration.
Guidelines for non-integration:
- Is information duplicated in the dataset? Then only one piece of information is needed
- Is the dataset integrated for a specific purpose, and some information is irrelevant?
- 



## Creating a suitable Linked Data Mapping
