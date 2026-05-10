# The Trinkwasserbrunnen dataset

The [Trinkwasserbrunnen dataset]() consists of the following data fields, which have been illustrated in the next table with some example rows:

| nummer  | trinkbrunnenart | bezirk | standort | postleitzahl | baujahr | einschraenkungen | informationen | thegeom |
|---|---|---|---|---|---|---|---|---|
| 1 | Kaiser |  Neukölln | Sangerhauser Weg/ Haselnussweg (am Britzer Garten nahe Rosengarten) | 12349 |  1985 | zur Zeit wegen Reparatur außer Betrieb | Betriebszeit: Mai bis Oktober, Link: https://www.bwb.de/de/trinkbrunnen.php | POINT(13.415048, 52.431351036930394) |
| 22 | Botsch | Charlottenburg-Wilmersdorf | Joachimsthaler Platz (nahe U-Bahnhof Kurfürstendamm) | 10719 | 2010  |  | Betriebszeit: Mai bis Oktober, Link: https://www.bwb.de/de/trinkbrunnen.php | POINT(13.330357999999814, 52.503411036854615) |
| 116 | Bituma | Tempelhof-Schöneberg | "Tempelhofer Feld (in der Nähe vom Eingang Südwest -Tempelhofer Damm) | 12101 | 2019 | | Betriebszeit: Mai bis Oktober, Link: https://www.bwb.de/de/trinkbrunnen.php | POINT(13.386741, 52.47160503688827)  |  


We can see that this dataset is in an open format (GeoJSON) and meets the 3-star criteria for linked open data. 
However, it lacks representation in RDF and links to other data resources to be a 5-star LOD.

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

For our Baumkataster example, a good classification might be sth. like **drinking water fountain**.

Bad classifications include:

- **pond**: Not specific enough

## Identifying and labeling instances

Every instance in the dataset should be identifiable by its own URI once the RDF conversion is complete.
To ensure the uniqueness of data identification, we need to ensure that the dataset includes a local identifier.
To that end, the preferred way is to find an identifier in our dataset.
Another alternative would be to generate another column with an identifier.

> [!IMPORTANT]
> In our dataset, we therefore look for a column that:
> - Has unique values
> - Has an indication of being an identifier from the column description, sth. like "id", "number", a.s.o.
> - Covers every instance of the dataset

| nummer | 
|---|
| 1 |  
| 22 |
| 116 | 

In the water fountain case, the only column that fulfills all aforementioned criteria is **nummer**, a unique ascending number assigned to each fountain.
The identifier will be combined with the previously defined data namespace to create URIs for the instances of the dataset.

```ttl
https://gdi-de.github.io/apworkshop2026_ldtutorial/1
https://gdi-de.github.io/apworkshop2026_ldtutorial/22
https://gdi-de.github.io/apworkshop2026_ldtutorial/116
```

### Instance labels

Local identifiers also make for good components of instance labels.
We might label a single water fountain merely "1", but maybe a better variant would be to name it "Brunnen {nummer}" in German and "water fountain {nummer}" in English.
We preserve the local identifier and add a more human-readable notion of a label at the same time

> [!NOTE]
> **CHOICE:** We treat baumnummer as the local identifier for this dataset. The identifier becomes part of the instance label in German and English. We choose the property [rdfs:label](http://www.w3.org/2000/01/rdf-schema#label) to designate the label because only one type of label is present.

**Resulting Mapping Definitions:**
```json
...
"id":"nummer,
...
"columns":{
    "nummer": {"propiri": "http://www.w3.org/2000/01/rdf-schema#label","prop": "anno","lang":"de", "prefix":"Brunnen "},
}
```

**Sample Triples:**
```ttl
@prefix gdidedata:<ttps://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix rdfs:<http://www.w3.org/2000/01/rdf-schema#> .

gdidedata:1 rdfs:label "Brunnen 1"@de, "water fountain 1"@en" .
gdidedata:22 rdfs:label "Brunnen 22"@de, "water fountain 22"@en" .
gdidedata:116 rdfs:label "Brunnen 116"@de, "water fountain 116"@en" .
```

## Treating columns with numbers

The Baumkataster dataset contains two columns whose values are exclusively numbers: **kronendurchmesser** and **stammdurchmesser**.
Both columns include doubles exclusively.

| postleitzahl | baujahr |
|---|---|
| 12349 | 1985 |
| 10719 | 2010 | 
| 12101 | 2019 |

The dataset itself cannot tell us anything about the context of these numbers in a machine-readable way.
However, we can analyze, from the column title, that a German zip code (**postleitzahl**) and a year designation is represented.

This clarifies two important things:

- Due to that fact that a German zip code is modeled in column **postleitzahl** we can infer an [xsd:integer](http://www.w3.org/2001/XMLSchema#integer) datatype of 5 digits length. Since we know that the semantics of the column is a zip code, we know that no unit needs to be assigned to values of this column. The column represents a postal identifier which happens to look like a quantity.
- Since we know that the **baujahr** column represents year, we can use the datatype [xsd:gYear](http://www.w3.org/2001/XMLSchema#gYear) is applicable for this column. Due to the usage of the type [xsd:gYear](http://www.w3.org/2001/XMLSchema#gYear) no other unit designation is needed.


> [!NOTE]
> **CHOICE:** Due to context knowledge we treat **postleitzahl** and **baujahr** as DataTypeProperties with range [xsd:integer](http://www.w3.org/2001/XMLSchema#integer) and [xsd:gYear](http://www.w3.org/2001/XMLSchema#gYear) respectively.
> We choose the IRI [locn:zipCode](http://www.w3.org/ns/locn#postCode) to describe the **postleitzahl** relation and [wdt:P571 (inception)](https://www.wikidata.org/prop/direct/P571) to described the **baujahr** column. We keep the German labels for both columns in the graph.

```json
"postleitzahl":{"propiri":"http://www.w3.org/ns/locn#postCode",
"range":"http://www.w3.org/2001/XMLSchema#integer",
"prop":"data"},

"baujahr": {"propiri": "http://www.wikidata.org/prop/direct/P571",
"proplabels":{"de":"Baujahr","en":"inception"},
"range":"http://www.w3.org/2001/XMLSchema#gYear",
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
@prefix locn: <http://www.w3.org/ns/locn#> .
@prefix wdt: <http://www.wikidata.org/prop/direct/> .

gdidedata:1 rdfs:label "Brunnen 1"@de, "drinking fountain 1"@en" .
gdidedata:1 locn:postCode "12349"^^xsd:integer .
gdidedata:1 wdt:P571 "1985"^^xsd:gYear .   
```

## Treating remaining String columns

Let's take a look at the remaining columns of this dataset.

| standort | einschraenkungen | informationen |
|---|---|---|
| Sangerhauser Weg/ Haselnussweg (am Britzer Garten nahe Rosengarten) | zur Zeit wegen Reparatur außer Betrieb | Betriebszeit: Mai bis Oktober, Link: https://www.bwb.de/de/trinkbrunnen.php |
| Joachimsthaler Platz (nahe U-Bahnhof Kurfürstendamm) |  | Betriebszeit: Mai bis Oktober, Link: https://www.bwb.de/de/trinkbrunnen.php |
| Tempelhofer Feld (in der Nähe vom Eingang Südwest -Tempelhofer Damm) | | Betriebszeit: Mai bis Oktober, Link: https://www.bwb.de/de/trinkbrunnen.php |  

The **standort** column provides a narrative description of the position of the drinking fountain which is often not precise enough to geocode.
The **einschraenkungen** column describes possible access restrictions of the drinking fountain.
The **informationen** column seems to provide additional information about the drinking fountain. Looking at its contents, it mostly provides the operation times of the drinking fountain. So one might think that the column is mislabeled.


> [!NOTE]
> **CHOICE:** We treat **standort** as a [xsd:string](http://www.w3.org/2001/XMLSchema#string) property with a German label and interpret it as a [skos:definition](http://www.w3.org/2004/02/skos/core#definition) since it defines in which situation the drinking fountain is located. 
> We treat **einschraenkungen** in the same way, i.e. [xsd:string](http://www.w3.org/2001/XMLSchema#string) property with a German label , but interpret it as a [skos:scopeNote](http://www.w3.org/2004/02/skos/core#scopeNote), defining the scope of usage for the drinking fountain.
> We treat **informationen** by taking its column description literally, despite the contents describing access times only. Hence, we use the [rdfs:comment](http://www.w3.org/2000/01/rdf-schema#comment) property to declare its contents additional information in German.


**Resulting Mapping Definitions:**
```json
"standort":{"propiri":"http://www.w3.org/2004/02/skos/core#definition",
"range":"http://www.w3.org/2001/XMLSchema#string",
"lang":"de","prop":"data"},
"einschraenkungen": {"propiri": "http://www.w3.org/2004/02/skos/core#scopeNote",
"proplabels":{"de":"Einschränkungen","en":"restrictions"},
"range":"http://www.w3.org/2001/XMLSchema#string",
"lang":"de",
"prop": "data"},
"informationen": {
"propiri": "http://www.w3.org/2000/01/rdf-schema#comment",
"range":"http://www.w3.org/2001/XMLSchema#string",
"lang":"de",
"prop": "data"}
```

**Resulting Sample Triple Representation**:
```ttl
@prefix gdidedata: <https://gdi-de.github.io/apworkshop2026_ldtutorial/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

gdidedata:1 rdfs:label "Brunnen 1"@de, "drinking fountain 1"@en" .
gdidedata:1 skos:definition "Sangerhauser Weg/ Haselnussweg (am Britzer Garten nahe Rosengarten)"@de .
            skos:scopeNote "zur Zeit wegen Reparatur außer Betrieb"@de .
            rdfs:comment "Betriebszeit: Mai bis Oktober, Link: https://www.bwb.de/de/trinkbrunnen.php"@de .
```

## Treating the Geometry

The geometry column can be converted using a variety of RDF vocabularies, each of which have different focal points.

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

gdidedata:1 rdfs:label "Brunnen 1"@de, "drinking water fountrain 1"@en" ;
                  geo:hasGeometry gdidedata:1_geom .
gdidedata:1_geom rdf:type sf:Point ;
                       rdfs:label "Geometry of drinking water fountain 1"@en, "Geometrie von Brunnen 1"@de ;
                       geo:asWKT "POINT(13.415048, 52.431351036930394)"^^geo:wktLiteral .
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

gdidedata:1_geom rdf:type sf:Point ;
                       rdfs:label "Geometry of drinking fountain 1"@en, "Geometrie des Brunnens 1"@en ;
                       geo:asWKT "POINT(13.415048, 52.431351036930394)"^^geo:wktLiteral .
```
