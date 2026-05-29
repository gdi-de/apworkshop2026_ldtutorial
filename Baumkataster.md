# The Baumkataster dataset

The [Baumkataster dataset](source/baumkataster_berlin.geojson) consists of the following data fields, which have been illustrated in the next table with some example rows:
 
| baumnummer  | kronendurchmesser | stammdurchmesser | art | baumart | gruenanlage | standortfkt | thegeom |
|---|---|---|---|---|---|---|---|
| GZAW870 |  12.0 |  48.0 | Weide | Salix spec.  |  Grünzug Altenwerder | sonst. öff. Flächen  |  POINT(9.918514827294882, 53.498226989745973) |
| GZAW883  | 6.0 | 19.0 | Spitz-Ahorn  | Acer platanoides  | Grünzug Altenwerder  |  sonst. öff. Flächen | POINT(9.918501999352625, 53.497818014776414) |
| MOBG657 |  5.0 | 32.0  |  Vogel-Kirsche | Prunus avium | Moorburg | sonst. öff. Flächen | POINT(9.91853784213246, 53.495375315136243)  |

We can see that this dataset is in an open format (GeoJSON) and meets the 3-star criteria for linked open data. 
However, it lacks representation in RDF and links to other data resources to be a [5-star LOD](https://www.w3.org/community/webize/2014/01/17/what-is-5-star-linked-data/).

To convert this dataset, we first need to gather some initial information about it:

A first analysis of the dataset should answer some of the following questions:

- Which datatype can we expect per column of this dataset?
  - Number: Integer e.g. 2, Double e.g. 4.0
  - Boolean: true/false, ja/nein, yes/no, on/off
  - String: Unique String, Categorized String?
 
This analysis can be done with a simple script that checks for uniqueness and data types. Even software like QGIS supports simple column detection.  

## The need for integration

Depending on the use case, not all information given inside the respective dataset is important for integration.
Guidelines for non-integration:

> [!IMPORTANT]
> Criteria for non-integration of columns
> - Is information duplicated in the dataset? Then only one piece of information is needed
> - Is the dataset integrated for a specific purpose, and some information is irrelevant?
> - Is some information of low quality and should be dismissed because of this?
> - Does the inclusion of the information violate laws, regulations, or licenses in the area where the dataset will be published?

Depending on those criteria, the number of dataset columns to work on may be reduced.

> [!NOTE]
> We have identified no columns in the dataset that are irrelevant for integration. With this in mind, we will proceed with a further analysis.

## How many instances per table row?

Having chosen the columns which should become part of the new RDF dataset, the question arises to how many RDF instances the dataset will resolve.

> [!IMPORTANT]
> - Create new instances when a group of columns described can be described as its own entity
> - Check if the new entity is related to the instance described by the whole column and name the entity appropriately

Suppose there is a dataset which contains a school and the address of said school with the columns, "schoolid", "schoolname", "street", "housenumber", "postcode", "city", "geometry".
One might model the school as follows:

```ttl
@prefix locn:<http://www.w3.org/ns/locn#> .
@prefix rdfs:<http://www.w3.org/2000/01/rdf-schema#> .
@prefix rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix ex:<http://example.org/> .
ex:myschool rdf:type wd:Q3914 ;
            rdfs:label "my school" ;
            locn:postcode "12345"^^xsd:integer ;
            locn:thoroughfare "my street" ;
            locn:locatorDesignator "1"^^xsd:integer ;
            locn:postName "Frankfurt am Main" .
```

However, one could also justify creating a new instance "address" that contains the address parts of the school instance.

```ttl
@prefix locn:<http://www.w3.org/ns/locn#> .
@prefix rdfs:<http://www.w3.org/2000/01/rdf-schema#> .
@prefix rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix ex:<http://example.org/> .
ex:myschool rdf:type wd:Q3914 ;
            rdfs:label "my school" ;
            locn:address ex:myschool_address .
ex:myschool_address rdf:type locn:Address .
            locn:postcode "12345"^^xsd:integer ;
            locn:thoroughfare "my street" ;
            locn:locatorDesignator "1"^^xsd:integer ;
            locn:postName "Frankfurt am Main" .
```

In this case, splitting the entities into school and address is chosen not only because of their semantics, but also because the existing vocabulary locn suggests it. The URI of the address instance extends the URI of the instance modeling the school. While this is not a requirement, it helps track instances in the knowledge graph.

> [!NOTE]
> For the Baumkataster dataset, we have identified no columns that need to become their own entities.
> Hence, only entities for the respective columns will be pursued in the following.

## Choosing an attribute and data namespace for the dataset

Before the dataset can be analyzed, we need to define two kinds of namespaces.

### Datanamespace

The data namespace is used to encode dataset instances after conversion. At best, this namespace can be resolved to at least an HTML and RDF serialization of the data instances.
Since this tutorial uses GitHub for data publication, it makes sense to use the GitHub Pages namespace of this repository for data conversion.
In a more professional setting, the hosting organization should provide a namespace, and data instances should be resolvable.

> [!NOTE]
> We choose **https://gdi-de.github.io/apworkshop2026_ldtutorial/** as our data namespace.

> [!CAUTION]
> In practice, it might be interesting to further categorize the namespace URL e.g. https://gdi-de.github.io/apworkshop2026_ldtutorial/**environment/tree** so that datasets of similar kinds will use the same namespace prefix. We will not discuss this issue further in this tutorial

### Vocabulary namespace

In an ideal setting, the vocabulary namespace is not needed. This is when every column can be mapped to an already existing vocabulary.
In practice, this is not always the case, which means missing vocabularies need to be defined to map all columns of a dataset, hence the need for a namespace.

> [!NOTE]
> We choose **[https://gdi-de.github.io/apworkshop2026_ldtutorial/ont#](https://gdi-de.github.io/apworkshop2026/ont#)** as our vocabulary namespace. 

> [!CAUTION]
> Publishing a vocabulary and managing an organization's vocabulary should be taken seriously. At best, people should discuss missing properties with their agency and work together to define missing vocabularies centrally or in a collaborative setting. Hosting a vocabulary in the same repository as the data it describes is usually a bad practice and is taken for this tutorial only for the sake of simplicity.

## Classification of the dataset

Here, we ask which semantic class to assign to the dataset.
We follow the following guidelines:

- Which Semantic class, that is sufficiently precise, describes all instances of the dataset?

For our Baumkataster example, a good classification might be sth. like **tree**.

Bad classifications include:

- **Plant**: Not specific enough
- **Oaks**: Does not cover the whole dataset
- **HamburgTrees**: Unnecessary high specificity and unnecessary mix of two different concepts in one class

#### How to create a classification for the concept **tree**?

The Semantic Web encourages us to reuse already existing definitions for **tree** if:

- They exist elsewhere in the Semantic Web already
- The definition matches our understanding of **tree**, which is valid for this dataset

**Defining a tree in the context of this dataset?**

- A wooden plant with leaves? (Bushes have leaves and wood....)
- A plant with a trunk and crown?

Defining sth. in a domain of which you are not an expert is not easy.

How about reusing a definition of Wikidata? [tree (Q10884)](https://www.wikidata.org/entity/Q10884)

> EN: perennial woody plant

> DE: eine verholzte Pflanze, die aus einer Wurzel, einem Stamm und einer Krone besteht

> [!NOTE]
> **CHOICE:** Reusing the Wikidata definition [tree (Q10884)](https://www.wikidata.org/entity/Q10884) including its own definition. We add a German label "Baum"@de and an English label "tree"@en

**Resulting Mapping Definitions:**
```json
...
"class": {"uri": "https://www.wikidata.org/entity/Q10884","labels": {"en": "tree","de": "Baum"}},
...
```

**Sample Triples:**
```ttl
@prefix wd: <http://www.wikidata.org/entity/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#label> .

wd:Q10884 rdfs:label "tree"@en, "Baum"@de .
```


## Identifying and labeling instances

Every instance in the dataset should be identifiable by its own URI once the RDF conversion is complete.
To ensure the uniqueness of data identification, we need to include a local identifier in the dataset.
To that end, the preferred way is to find an identifier in our dataset.
Another alternative would be to create a new column with an identifier.

> [!IMPORTANT]
> In our dataset, we therefore look for a column that:
> - Has unique values
> - Has an indication of being an identifier from the column description, sth. like "id", "number", a.s.o.
> - Covers every instance of the dataset

| baumnummer | 
|---|
| GZAW870 |  
| GZAW883 |
| MOBG657 | 

In the Baumkataster case, the only column that fulfills all aforementioned criteria is **baumnummer**, a unique number assigned to each tree.
The identifier will be combined with the previously defined data namespace to create URIs for dataset instances.

```ttl
https://gdi-de.github.io/apworkshop2026_ldtutorial/GZAW870
https://gdi-de.github.io/apworkshop2026_ldtutorial/GZAW883
https://gdi-de.github.io/apworkshop2026_ldtutorial/MOBG657
```

### Instance labels

Local identifiers also make for good components of instance labels.
We might label a single tree merely "GZAW870", but maybe a better variant would be to name it "Baum {baumnummer}" in German and "tree {baumnummer}" in English.
We preserve the local identifier and add a more human-readable notion of a label at the same time.

> [!CAUTION]
> Many vocabularies provide properties for labels. Despite rdfs:label being the most prominent choice, the correct choide of a label property depends on several factors:
> - Is it one label or are there many? For many, consider e.g. [skos:prefLabel](http://www.w3.org/2004/02/skos/core#prefLabel) [skos:altLabel](http://www.w3.org/2004/02/skos/core#altLabel)
> - Is the label to be considered a title? Then maybe a more specific property like [dc:title](http://purl.org/dc/terms/title) is a better choice
> - Is it a very specific label which makes sense only in a specific context? Consider extending rdfs:label to create your own specific label property 

> [!NOTE]
> **CHOICE:** We treat baumnummer as the local identifier for this dataset. The identifier becomes part of the instance label in German and English. We choose the property [rdfs:label](http://www.w3.org/2000/01/rdf-schema#label) to designate the label because only one type of label is present.

**Resulting Mapping Definitions:**
```json
...
"id":"baumnummer,
...
"columns":{
    "baumnummer": {"propiri": "http://www.w3.org/2000/01/rdf-schema#label","prop": "anno","lang":"de", "prefix":"Baum "},
}
```

**Sample Triples:**
```ttl
@prefix gdidedata:<ttps://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix rdfs:<http://www.w3.org/2000/01/rdf-schema#> .

gdidedata:GZAW870 rdfs:label "Baum GZAW870"@de, "tree GZAW870"@en" .
gdidedata:GZAW883 rdfs:label "Baum GZAW883"@de, "tree GZAW883"@en" .
gdidedata:MOBG657 rdfs:label "Baum MOBG657"@de, "tree MOBG657"@en" .
```

## How to find appropriate vocabularies?

A lot of RDF vocabularies have already been defined for standard use cases such as:
- Modeling of vocabularies (VOAF)
- Bibliography management (BIBO)
- Metadata descriptions (DublinCore)

> [!IMPORTANT]
> A rule of thumb:
> - The more commonplace the use case seems to be, the more likely it is that a vocabulary has already been defined.
> - Example I: "I want to represent geometries in RDF" - This does not seem uncommon... a vocabulary likely exists
> - Example II: "I have my own classification of objects that I did never share with the world" - Very unlikely that a vocabulary exists unless you defined and published it yourself

To find vocabularies some homepages provides search capabilities:
- [BARTOC](https://bartoc.org/)
- [LOV: Linked Open Voacbularies](https://lov.linkeddata.es/dataset/lov/about)
- Several W3C speccifications like [RDF](https://www.w3.org/TR/1999/REC-rdf-syntax-19990222/), [RDFS](https://www.w3.org/TR/rdf-schema/), [OWL](https://www.w3.org/TR/owl2-overview/), [SHACL](https://www.w3.org/TR/shacl/), Profiles

In addition, it makes sense to check already existing databases for URIs of definitions that can be reused:
- [Wikidata](https://www.wikidata.org/)
- [DBPedia](https://www.dbpedia.org/)
- [LinkedGeoData](https://linkedgeodata.org/)

We will use some of these mentioned resources in the following mapping of the dataset to RDF.

## Treating columns with numbers

The Baumkataster dataset contains two columns whose values are exclusively numbers: **kronendurchmesser** and **stammdurchmesser**.
Both columns include doubles exclusively.

| kronendurchmesser | stammdurchmesser |
|---|---|
|  12.0 | 48.0 |
| 6.0 | 19.0 | 
|  5.0 | 32.0 |

Unfortunately, the dataset itself cannot tell us about the context of these number columns.

While the word "Durchmesser", diameter, would suggest a form of length measurement, we have no idea which measurement unit to apply.

This is where the mapping to linked open data needs to include information about the measurement unit, which may be derived from dataset documentation, the dataset provider, or plausibility checks by cross-referencing other datasets of similar kinds.

> [!CAUTION]
> Many vocabularies compete to represent measurement units, but the most prominent of them are ordered by popularity:
> - [QUDT (Quantities Units, Dimensions and Measurements)](https://www.qudt.org)
> - [OM (Ontology for Units of Measurements)](http://www.ontology-of-units-of-measure.org)
> - OGC Registry 

> [!IMPORTANT]
> - How are tree trunk diameters usually measured? Does it make sense to have a tree trunk diameter unit of km?
> - Dataset context: Can we expect the measurements to be in the metric system?
> - Is our dataset historic/from archaeology with ancient measurements, or does it have specific requirements on the representation of measurements as mandated by a government regulation?

Such treatments need to be applied to every column, including numeric ones, since each column could potentially describe sth in a given unit.

> [!NOTE]
> **CHOICE:** We treat **kronendurchmesser** and **stammdurchmesser** as DataTypeProperties with range [xsd:double](http://www.w3.org/2001/XMLSchema#double). kronendurchmesser will be assigned the unit [om:meter](http://www.ontology-of-units-of-measure.org/resource/om-2/meter) and stammdurchmesser the unit [om:centimetre](http://www.ontology-of-units-of-measure.org/resource/om-2/centimeter). Wikidata does not define properties for tree trunk diameter and tree crown diameter; only a diameter property [P2547](http://www.wikidata.org/prop/direct/P2547), which is not suitable for inclusion due to its lack of specificity. Two new properties, therefore, need to be created in a separate vocabulary.

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

**Sample triples:**
```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix gdideont: <https://gdi-de.github.io/apworkshop2026_ldtutorial/ont#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix om: <http://www.ontology-of-units-of-measure.org/resource/om-2/> .

gdidedata:GZAW870 rdfs:label "Baum GZAW870"@de, "tree GZAW870"@en" .
gdidedata:GZAW870 gdideont:kronendurchmesser gdidedata:GZAW870_kronendurchmesser .
                  gdideont:stammdurchmesser gdidedata:GZAW870_stammdurchmesser .
gdidedata:GZAW870_kronendurchmesser rdf:type om:Measure ;
                                    rdfs:label "Measurement of Kronendurchmesser of tree GZAW870"@en, "Kronendurchmesser von Baum GZAW870"@de ;
                                    om:hasUnit om:meter ;
                                    om:hasValue "12.0"^^xsd:double .
gdidedata:GZAW870_stammdurchmesser rdf:type om:Measure ;
                                    rdfs:label "Measurement of Stammdurchmesser of tree GZAW870"@en, "Stammdurchmesser von Baum GZAW870"@de ;
                                    om:hasUnit om:centimeter ;
                                    om:hasValue "48.0"^^xsd:double .    
```

## Treating category String columns

Category string columns suggest some form of subcategorization.

> [!IMPORTANT]
>Questions to consider are:
> - How does the subcategorization relate to the chosen dataset classification?
> - Does the column actually subclassify a tree, or does it subclassify sth. else?
> - Can we relate the subclassification to a URI schema from a data repository already existing in the Semantic Web?

In our dataset, we have two columns that fit this category: **art** and **baumart**.

**art** is a broader tree categorization, **baumart** is a more scientific tree categorization based on Latin designations.

The options here are as follows:
- Treat both columns as categorizations, i.e., a tree will be classified with a broader concept and a more specific concept
- Treat only the more specific concept as a subclassification and the other column as a categorization independent of the classification of the dataset

To allow for categorizations, a mapping of String values to concept URIs needs to be created:
| baumart  | concept | label_en | label_de |
|---|---|---|---|
| Quercus robur | [Q165145](https://www.wikidata.org/entity/Q165145) |  Quercus robur | Stieleiche |
| Salix alba | [Q156918](https://www.wikidata.org/entity/Q156918) | white willow | Silber-Weide |

> [!NOTE]
> **CHOICE:** We create a mapping of column values to Wikidata concepts, which allows the unique description of the semantics of the respective column's contents. The concepts are related to the instance using the [rdf:type](http://www.w3.org/1999/02/22-rdf-syntax-ns#type) property. Each concept also becomes a subclass of the class(es) chosen as the classification of the dataset, as signified with the [rdfs:subClassOf](http://www.w3.org/2000/01/rdf-schema#subClassOf) property. We define German and English labels per mapped concept. German labels are taken from the dataset itself, English labels are additionally defined.

**Resulting Mapping Definitions:**
```json
...
"art": {"propiri": "http://www.w3.org/2000/01/rdf-schema#subClassOf","prop": "subclass","order": 1,"valuemapping":{
    "Amberbaum":"https://www.wikidata.org/entity/Q469652",
    "Amerikanischer Amberbaum":"https://www.wikidata.org/entity/Q469652",
    "Eberesche":{"uri":"https://www.wikidata.org/entity/Q146198","labels":{"en":"rowan","de":"Eberesche"}},
    "Eiche":{"uri":"https://www.wikidata.org/entity/Q12004","labels":{"en":"oak","de":"Eiche"}},
    ...
  }
},
"baumart": {"propiri": "http://www.w3.org/2000/01/rdf-schema#subClassOf","prop": "subclass","valuemapping":{
 "Prunus avium":"https://www.wikidata.org/entity/Q165137",
 "Populus alba":"https://www.wikidata.org/entity/Q146269",
 "Acer platanoides":"https://www.wikidata.org/entity/Q26745",
  ....
 }
},
...
```

**Sample triples:**
```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix wd: <http://www.wikidata.org/entity/>
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

#Inclusion of Wikidata type for Quercus rubur: Stieleiche as subclass of tree
wd:Q166145 rdfs:subClassOf  wd:Q10884 ;
           rdfs:label "Quercus robur"@en, "Stieleiche"@de .          

# Classification of tree
gdidedata:GZAW870 rdfs:label "Baum GZAW870"@de, "tree GZAW870"@en" .
gdidedata:GZAW870 rdf:type wd:Q10884 .

# Typing tree instance as Quercus rubur
gdidedata:GZAW870 rdf:type wd:Q166145 .

```

## Treating unique String columns

None of the columns of the given dataset are unique String columns, so a treatment of these columns does not apply here.

## Treating remaining String columns

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
> **CHOICE:** For the sake of simplicity in this example, we treat **standortfkt** as a German comment and **gruenanlage** as a German [xsd:string](http://www.w3.org/2001/XMLSchema#string). We choose the Wikidata Property [P3018](http://www.wikidata.org/prop/direct/P3018) which describes a protected area as the identifier for the green area described by **gruenanlage**

**Resulting Mapping Definitions:**
```json
"gruenanlage": {
"propiri":"http://www.wikidata.org/prop/direct/P3018",
"range":"http://www.w3.org/2001/XMLSchema#string",
"lang":"de",
"proplabels":{"de":"Grünanlage","en":"green area"},
"prop": "data"},
"standortfkt": {"prop": "data","propiri":"http://www.w3.org/2000/01/rdf-schema#comment","lang":"de","range":"http://www.w3.org/2001/XMLSchema#string"}
```

**Resulting Sample Triple Representation**:
```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix wdt: <http://www.wikidata.org/prop/direct/> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

gdidedata:GZAW870 rdfs:label "Baum GZAW870"@de, "tree GZAW870"@en" .
gdidedata:GZAW870 rdf:type wd:Q10884 .
gdidedata:GZAW870 rdfs:comment "sonst. öff. Flächen"@de" .
gdidedata:GZAW870 wdt:P3018 "Grünzug Altenwerder"@de .
```

## Treating the Geometry

The geometry column can be converted using a variety of RDF vocabularies, each with a different focus.

[Annex E](https://opengeospatial.github.io/ogc-geosparql/geosparql11/document.html#_8ebafca6-d4a4-aefa-9338-3a691278375e) of the GeoSPARQL 1.1 specification lists 16 different vocabularies besides GeoSPARQL which have been used to encode Geometries in RDF.

> [!IMPORTANT]
> We will focus on GeoSPARQL 1.1 integration here for the following reasons:
> - GeoSPARQL 1.1 is an official OGC standard
> - GeoSPARQL 1.1 is the only RDF standard to support different CRS systems
> - GeoSPARQL 1.1 supports all geometry types available in the GML and (Extended) Well-Known Text Specifications

However, if only WGS84 coordinates are to be encoded, GeoSPARQL is not necessarily needed.

```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix wd: <http://www.wikidata.org/entity/> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix geo: <http://www.opengis.net/ont/geosparql#> .
@prefix sf: <http://www.opengis.net/ont/sf#> .

gdidedata:GZAW870 rdfs:label "Baum GZAW870"@de, "tree GZAW870"@en" ;
                  rdf:type wd:Q10884 ;
                  geo:hasGeometry gdidedata:GZAW870_geom .
gdidedata:GZAW870_geom rdf:type sf:Point ;
                       rdfs:label "Geometry of tree GZAW870"@en, "Geometrie von Baum GZAW870"@de ;
                       geo:asWKT "POINT(9.918514827294882, 53.498226989745973)"^^geo:wktLiteral .
```
GeoSPARQL requires creating two instances per tree in the case of this particular dataset.
In one instance, the tree itself is a subclass of geo:Feature, and represents the geospatial feature component.
The other instance represents the geometry and its potentially many serializations.

### Choosing an appropriate GeoSPARQL serialization

GeoSPARQL 1.1 provides serializations of Geometries in the following formats:
- Well-Known Text + CRS
- GML
- KML (only WGS84 as per format definition)
- GeoJSON (only WGS84 as per format definition)
- DGGS

The choice of serialization will depend on the need for the representation of different CRS, on compatibility considerations with applications and/or the web and finally the needs of the use case.

> [!NOTE]
> **CHOICE:** We choose the Well-Known Text serialization for this tutorial since it is the most common serialization and supports different CRS.

```ttl
gdidedata:GZAW870_geom rdf:type sf:Point ;
                       rdfs:label "Geometry of tree GZAW870"@en, "Geometrie von Baum GZAW870"@de ;
                       geo:asWKT "POINT(9.918514827294882, 53.498226989745973)"^^geo:wktLiteral .
```

**Resulting Mapping Definitions:**
```json
...
"epsg:"EPSG:4326",
"geomliteral":"WKT"
...,
```

**Sample Triples:**
```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix wd: <http://www.wikidata.org/entity/>
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix geo: <http://www.opengis.net/ont/geosparql#> .
@prefix sf: <http://www.opengis.net/ont/sf#> .      

gdidedata:GZAW870 rdfs:label "Baum GZAW870"@de, "tree GZAW870"@en" .
gdidedata:GZAW870 rdf:type wd:Q10884 ;
                  geo:hasGeometry gdidedata:GZAW870_geom .

gdidedata:GZAW870_geom rdf:type sf:Point ;
                       rdfs:label "Geometry of tree GZAW870"@en, "Geometrie des Baums GZAW870"@en ;
                       geo:asWKT "POINT(9.918514827294882, 53.498226989745973)"^^geo:wktLiteral .
```

### Representing the spatial reference system

Every geometric representation is associated with a spatial reference system that describes its location on Earth or on another interstellar object.

While many formats exist for representing CRS information, e.g., PROJ4, PROJJSON, and WKT, a native RDF representation of CRS elements is currently being developed in an [OGC working group](https://github.com/opengeospatial/ontology-crs).

The proposed ontology allows to represent the following elements of a CRS, each as a typed RDF instance in a knowledge graph:
- The Coordinate Reference System
- The Datum
- The Coordinate System
- Coordinate Transformations
- Projection Types
- Planets and Spheroids

The coordinate reference systems involved in this example have already been described using CRS definitions, so that a conversion to RDF according to the vocabulary of the OGC CRS working group can be performed.

> [!CAUTION]
> Since the work of the OGC working group is still ongoing, changes regarding the namespace and certain elements of the contents of the vocabulary definition could change.
> We are aware of this in this tutorial and will update the definitions for a real-world use case in line with developments from the standardization group.

Given an RDF definition of a CRS, the question remains of how to relate the CRS definition to the actual geometries that are defined in the knowledge graph.
The proposed geo:inSRS property of GeoSPARQL aims to aid in that regard.

```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix geo: <http://www.opengis.net/ont/geosparql#> .
@prefix sf: <http://www.opengis.net/ont/sf#> .      

gdidedata:1_geom rdf:type sf:Point ;
                       rdfs:label "Geometry of drinking fountain 1"@en, "Geometrie des Brunnens 1"@en ;
                       geo:inSRS <http://www.opengis.net/def/crs/EPSG/0/4326> ;
                       geo:asWKT "POINT(13.415048, 52.431351036930394)"^^geo:wktLiteral .
```

> [!NOTE]
> **CHOICE:** Despite the CRS vocabulary still being in draft by the OGC, we use the RDF representation to add information about the CRS system used for our geometries.
> We connect the information using the proposed geo:inSRS property of GeoSPARQL.

## Adding additional non-existent information

Besides, e.g., missing units or other information implicitly given in the dataset's data structure, other information needed to characterize linked open data is missing entirely from the dataset.

For a linked open dataset to be comprehensively described, this information would need to be gathered from accompanying PDF documentation or asked for by the respective authorities.

### Provenance information

Provenance information can be added to the dataset using specific vocabularies such as [PROV-O](https://www.w3.org/TR/prov-o/) provenance ontology.
PROV-O promises to capture the dataset's creation process.

<img width="2625" height="1480" alt="provo" src="https://github.com/user-attachments/assets/f587c876-8333-4076-87e8-2a75e3859b95" />

The provenance ontology defines three different entities:

- [prov:Entity](http://www.w3.org/ns/prov#Entity): An entity is a physical, digital, conceptual, or other kind of thing with some fixed aspects; entities may be real or imaginary.
- [prov:Activity](http://www.w3.org/ns/prov#Activity): An activity is something that occurs over a period of time and acts upon or with entities; it may include consuming, processing, transforming, modifying, relocating, using, or generating entities.
- [prov:Agent](http://www.w3.org/ns/prov#Agent): An agent is something that bears some form of responsibility for an activity taking place, for the existence of an entity, or for another agent's activity.

This means the vocabulary helps establish WHO created a dataset (ENTITY) and the creation process (ACTIVITY).

Let's apply this methodology to the Baumkataster dataset:

```

```

### Licensing

Every dataset should include license information, which can be represented in the linked open data graph.
There are two choices for the representation of licenses:
- Represent the original dataset itself and add a license statement
- Attach a license statement to every instance of the linked data graph publication

To attach a license statement to instances of the knowledge graph, the license itself must be identified by a URI.
This is the case for many common licenses already, but it should be checked for the specific license used.

For example, all Creative Commons licenses can be addressed using a URI like [CC BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0)

For the Trinkwasserbrunnen dataset we follow the license given in its metadata, which is the [Datenlizenz Deutschland – Namensnennung – Version 2.0](https://www.govdata.de/dl-de/by-2-0)

```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix dc:<http://purl.org/dc/elements/1.1/>
gdidedata:1 dc:license <https://www.govdata.de/dl-de/by-2-0> .
```

While the aforementioned example references individual triples, the dataset itself may also be modeled in RDF, so that the license information can be placed at the dataset instance. To model the dataset, the [DCAT vocabulary](https://www.w3.org/TR/vocab-dcat-3/) can be used. 

```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix void: <http://rdfs.org/ns/void#> .
@prefix dcat: <http://www.w3.org/ns/dcat#> .

gdidedata:fountain_ds rdf:type dcat:Dataset ;
                  rdfs:label "Dataset of drinking fountains"@en ;
                  dcat:distribution gdidedata:fountain_ds_dist .
gdidedata:fountain_ds_dist rdf:type dcat:Distribution ;
                       rdfs:label "Distribution of the Dataset of drinking fountains"@en ;
                       dc:license <https://www.govdata.de/dl-de/by-2-0> .
gdidedata:1 void:inDataset gdidedata:fountain_ds .
```

> [!NOTE]
> **CHOICE:** We choose to attach a license statement to every instance and, for the sake of simplicity, do not model the dataset itself. The example given in this section should provide enough information for a user to take this step.
> For the Trinkwasserbrunnen dataset we use the [Datenlizenz Deutschland – Namensnennung – Version 2.0](https://www.govdata.de/dl-de/by-2-0)  which is mandated by the [source data](https://gdi.berlin.de/geonetwork/srv/api/records/8aaca41b-d665-447a-93ec-b76e431852fc) 

**Resulting Mapping Definitions:**
```json
"license:"https://www.govdata.de/dl-de/by-2-0",
```

### Dataset metadata
The metadata of the given dataset might also be integrated into the linked open data graph.
The prerequisite for that is a metadata format that is represented in, or can be represented in, RDF.

For the dataset here, we can find DCAT-AP formatted metadata in TTL, which can be directly integrated into the knowledge graph with only on prerequisite.
The corresponding metadata file has been added to the source folder of this repository.
The data instances need to be made aware of the DCAT-AP metadata description and in particular its URI.
With this addition in the knowledge graph, the files may simply be integrated into the already existing knowledge graph.


## Summarizing the Linked Data Mapping

We summarize the insights gained by answering the aforementioned questions in a linked open data mapping file, which will form the basis for the conversion of the source file to an RDF representation.

The mapping files can be found in this repository as [mappings/baumkataster.json](mappings/baumkataster.json) and [mappings/trinkwasserbrunnen.json](mappings/trinkwasserbrunnen.json) respectively.

The RDF conversion can be implemented in a programming language of choice, but for simplicity, we reuse the [geordfconverter](https://github.com/situx/geordfconverter/) tool.
