# apworkshop2026_ldtutorial
Tutorial Linked Open Data AP Workshop 2026
  
This tutorial showcases the conversion of two Geodata sets retrieved from German geospatial agency websites into RDF (Linked Open Data) and how to handle the resulting data in practice. 

## Use Case 1: Baumkataster Hamburg (cadastre of trees alongside a street)
Trees in Germany are numbered and classified according to the type of tree, their position within an urban environment, and, finally, certain parameters that help characterize their shape.
 
[Use Case 1](Baumkataster.md) examines how to convert these parameters into Linked Open Data.

### Conversion results



## Use Case 2: Trinkwasserbrunnen Berlin (drinking water fountains)

Drinking water fountains are a common occurrence in many German cities, including Berlin.

[Use Case 2](Trinkwasserbrunnen.md) Drinking water fountains are classified by type, origin, accessibility, and location.

### Conversion results

## Publishing newly defined vocabularies

During the dataset mapping process, a few terms could not be mapped to existing vocabularies.
However, their integration into the linked data deployments seemed important enough not to drop them.

For these terms, a new vocabulary definition needs to be created.

> [!IMPORTANT]
> A vocabulary will be represented as an RDF file, which only includes:
> - rdfs:Class or [owl:Class](http://www.w3.org/2002/07/owl#Class) instances with at least one label and one definition
> - [owl:DataTypeProperty](http://www.w3.org/2002/07/owl#DataTypeProperty), [owl:ObjectProperty](http://www.w3.org/2002/07/owl#ObjectProperty) or [owl:AnnotationProperty](http://www.w3.org/2002/07/owl#AnnotationProperty) instances with at least one label and one definition
> - Constraints on the aforementioned instances using [OWL](https://www.w3.org/TR/owl-features/) restrictions or [SHACL](https://www.w3.org/TR/shacl/) shapes

Generally, all published vocabularies should adhere to the 
[Best Practices for Implementing FAIR Vocabularies and Ontologies on the Web](https://dgarijo.com/papers/best_practices2020.pdf)

For the sake of this example, we will discuss only selected details of how these principles are implemented here.

[!IMPORTANT]
> Important aspects when publishing a linked open data vocabulary:
> - Choosing a non-common prefix for the vocabulary. Check existing prefixes, e.g., on [prefix.cc](http://prefix.cc) and mark this prefix with a description in the [VANN vocabulary for annotating vocabulary descriptions](https://vocab.org/vann/)
> - 

We then use the software [pyLODE](https://github.com/rdflib/pyLODE) to render the generated vocabulary on an HTML page and to make it dereferencable for human users.

In both datasets, we have defined a vocabulary namespace of [https://github.com/gdi-de/apworkshop2026_ldtutorial/ont#](https://gdi-de.github.io/apworkshop2026_ldtutorial/ont#)


## Publishing Linked Open Data

The two use cases illustrated how to create a linked open data serialization of the respective data in RDF.
It also showed how a mapping from a non-RDF dataset to an RDF dataset could be created, encoded, and saved.

Relevant literature:

- [Linked Open Data Best Practices](https://www.w3.org/TR/ld-bp/)
- [Spatial Data On The Web Best Practices](https://www.w3.org/TR/sdw-bp/)

### Choosing a publication deployment setting

The next step after the conversion process succeeds is to create a deployment of web resources for all data instances in the linked open data set.

Works to consider here are:
- [Linked Open Usable Data](https://linked.art/loud/)
- [Linked Open Usable Data Dumps](https://eceasst.org/index.php/eceasst/article/view/2630)

> [!IMPORTANT]
>Questions to consider are:
> - Which data deployments per instance should be published? Recommended would be at least one human-readable and one machine-readable version in RDF.
> - Should (static) API access be provided?
> - How should Linked Open Data be accessed? Only file-based? Using a query service? Using triple pattern fragments?

In this example, we publish on GitHub Pages, which does not allow us to easily set up a query service or a triple-pattern fragments service.
Hence, we decided to pursue a data dump-based approach. This decision might vary depending on resource availability.

> [!NOTE]
> We have chosen the following publication modes for the dataset.
> - HTML for human readability
> - [TTL](https://www.w3.org/TR/turtle/) and [JSON-LD](https://www.w3.org/TR/json-ld11/) as [RDF](https://www.w3.org/TR/rdf11-concepts/) serializations

#### Choosing target communities for linked data publication

To determine the correct linked open usable data deployment, we need to infer the deployment's users.

> [!IMPORTANT]
> Which community of users do we anticipate will use this linked open data set?
> Which data formats/data services do people in these communities commonly use?
> Do we need to consider special laws and/or regulations when publishing linked data for this community?

For the two datasets, we cannot pinpoint a specific user base beyond people interested in using geodata.
There are likely use cases in administration and beyond, but for the sake of this tutorial, we cannot make a more precise assessment.

We therefore answer the aforementioned questions for a community of users working with geospatial data.

Typical GIS users are not necessarily familiar with the linked open data paradigm, but are familiar with the following forms of publication:
- Geospatial data formats (e.g., [GeoJSON](https://datatracker.ietf.org/doc/html/rfc7946))
- Geospatial APIs, e.g., [OGC API Features](https://ogcapi.ogc.org/features/) 

> [!NOTE]
> We publish geospatial features as a single GeoJSON file, with one feature per instance.
We also publish a static [OGC API Features](https://ogcapi.ogc.org/features/) deployment so that the geodata, modeled as linked open data, can be accessed in GIS applications.

### Dereferencing URIs

URIs for instances in the dataset can be dereferenced using the previously mentioned deployments.

Take, for instance, the URI for one of the trees of the tree dataset:

```
https://gdi-de.github.io/apworkshop2026_ldtutorial/GZAW870
```


#### Content Negotiation

[HTTP Content Negotiation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Content_negotiation) 
is a negotiation process between a web browser and a web server to agree on the kind of format that will be submitted as the result of an HTTP request.
This means we could have a client-side request for an RDF serialization vs. another for HTML.

Common HTTP web servers like Apache or NGINX allow for the configuration of content negotiation, which is currently not enabled on GitHub Pages.

Therefore, on GitHub pages webspaces, it is necessary to state the format by means of the URL itself, e.g., https://gdi-de.github.io/apworkshop2026_ldtutorial/GZAW870/index.html or https://gdi-de.github.io/apworkshop2026_ldtutorial/GZAW870/index.ttl

### Machine-readable linked data set description using VoID

The [Vocabulary of Interlinked Datasets (VoID)](https://www.w3.org/TR/void/) is a vocabulary to describe metadata about RDF datasets.
Compared to metadata such as DCAT, which describes context, licenses, and further information about the data publishing and serving process, VoID describes statistics, access metadata, and structural metadata, as well as the nature, structure, and links between multiple datasets.
Hence, data discovery within an RDF ecosystem is greatly enhanced.


## Querying Linked Open Data 

For this tutorial, querying linked open data through a [SPARQL](https://www.w3.org/TR/sparql11-query/) endpoint service is not readily possible, because, as a standalone showcase, only a static webspace has been provided.

However, linked data files may still be queried using client software.

### SPARQL 1.0/1.1 or GeoSPARQL 1.0/1.1?

The SPARQL query language allows users to query linked open data publications and provides a variety of in-query operations to take advantage of.
In particular, SPARQL allows:
- Filter triples
- Use predicate paths for easier navigation
- Order results
- Group results

The SPARQL query language has no support for in-query operations on geospatial data, such as geometry intersection and union, or for checking these capabilities.
To support such operations, a GeoSPARQL-compatible implementation needs to be used.

### Querying linked open data using JavaScript

The HTML deployment of this repository includes a [JavaScript SPARQL Query page](https://gdi-de.github.io/apworkshop2026_ldtutorial/sparql.html?endpoint=https://gdi-de.github.io/apworkshop2026_ldtutorial/) that, by default, loads the entire generated RDF dataset.
The query capabilities of this JavaScript Query engine rely on the software library [comunica](https://comunica.dev)

<img width="1885" height="1034" alt="image" src="https://github.com/user-attachments/assets/47e18029-b201-4e28-a75f-50a3293e6530" />

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT * WHERE {
  <https://gdi-de.github.io/apworkshop2026/42> ?rel ?val .
  OPTIONAL{?val rdfs:label ?valLabel .}
} LIMIT 100
```
This SPARQL query returns all information associated with the drinking water fountain with ID 42.
It only uses SPARQL 1.1 capabilities and does not need GeoSPARQL support.

### Querying linked open data in Python

Python scripts may use the library [rdflib](https://rdflib.readthedocs.io/en/stable/) to query RDF data.

```python
from rdflib import Graph

# Create a Graph
g = Graph()

# Parse in an RDF file hosted on the Internet
g.parse("http://www.w3.org/People/Berners-Lee/card")

# Loop through each triple in the graph (subj, pred, obj)
for subj, pred, obj in g:
    # Check if there is at least one triple in the Graph
    if (subj, pred, obj) not in g:
       raise Exception("It better be!")

# Print the number of "triples" in the Graph
print(f"Graph g has {len(g)} statements.")
# Prints: Graph g has 86 statements.

# Print out the entire Graph in the RDF Turtle format
print(g.serialize(format="turtle"))
```

### Querying linked open data in QGIS

Support for linked open data resources in QGIS is given by the SPARQLing Unicorn QGIS plugin.
