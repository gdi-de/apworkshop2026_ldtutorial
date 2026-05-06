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

**Defining a tree in the context of this dataset?**

- A wooden plant with leaves? (Bushes have leaves and wood....)
- A plant with a trunk and crown?

Defining sth. in a domain of which you are not an expert is not easy.

How about reusing a definition of Wikidata? [tree (Q10884)](https://www.wikidata.org/wiki/Q10884)

> EN: perennial woody plant

> DE: eine verholzte Pflanze, die aus einer Wurzel, einem Stamm und einer Krone besteht

RESULT: Reusing the Wikidata definition [tree (Q10884)](https://www.wikidata.org/wiki/Q10884)

#### Identifying instances

Every instance in the dataset should be identifiable by its own URI once the RDF conversion is complete.
To ensure the uniqueness of data identification, we need to ensure that the dataset includes a local identifier.
To that end, the preferred way is to find an identifier in our dataset.
Another alternative would be to generate another column with an identifier.

In our dataset, we therefore look for a column in our dataset that:
- Has unique values
- Has an indication of being an identifier from the column description, sth. like "id", "number", a.s.o.
- Covers every instance of the dataset

In the Baumkataster case, the only column that fulfills all aforementioned criteria is "baumnummer", a unique number assigned to each tree.
We will keep this identifier in mind and apply it in a later step.

#### Treating columns with numbers

The Baumkataster dataset contains two columns whose values are exclusively numbers: **kronendurchmesser** and **stammdurchmesser**.
Both columns include double numbers.

Unfortunately, the dataset itself cannot tell us about the context of these number columns.

While the word "Durchmesser", diameter, would suggest a form of length measurement, we have no idea which measurement unit to apply.

This is where the mapping to linked open data needs to contain information about the measurement unit, which may be derived either from a dataset documentation, the dataset provider or from plausibility checks by crossreferencing other datasets of similar kinds.

- How are tree trunk diameters usually measured? Does it make sense to have a tree trunk diameter unit of km?

Such treatments need to be done for every column including a number, since every column could potentially describe sth. in a given unit.

#### Treating category String columns

#### Treating unique String columns

#### Treating remaining String columns





### Creating a suitable Linked Data Mapping
