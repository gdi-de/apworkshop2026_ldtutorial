## The Baumkataster dataset

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

### Examination of data fields

Let's investigate the data fields and any other information we need for conversion in this section.

#### Classification of the dataset

Here, we ask which semantic class to assign to the dataset.
We follow the following guidelines:

- Which Semantic class, that is sufficiently precise, describes all instances of the dataset?

For our Baumkataster example, a good classification might be sth. like **tree**.

Bad classifications include:

- **Plant**: Not specific enough
- **Oaks**: Does not cover the whole dataset
- **HamburgTrees**: Unnecessary high specificity and unnecessary mix of two different concepts in one class

##### How to create a classification **tree**?

The Semantic Web encourages us to reuse already existing definitions for **tree** if:

- They exist elsewhere in the Semantic Web already
- The definition matches our understanding of **tree** which is valid for this dataset

**What do we think a tree is in the context of this dataset?**


### Creating a suitable Linked Data Mapping
