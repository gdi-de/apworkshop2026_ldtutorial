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