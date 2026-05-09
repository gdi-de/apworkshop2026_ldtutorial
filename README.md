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

Generally, all published vocabularies should adhere to the [Best Practices for Implementing FAIR
Vocabularies and Ontologies on the Web](https://dgarijo.com/papers/best_practices2020.pdf)

## Publishing Linked Open Data

The two use cases illustrated how to create a linked open data serialization of the respective data in RDF.
It also showed how a mapping from a non-RDF dataset to an RDF dataset could be created, encoded, and saved.

Relevant literature:

- [Linked Open Data Best Practices](https://www.w3.org/TR/ld-bp/)
- [Spatial Data On The Web Best Practices](https://www.w3.org/TR/sdw-bp/)
- 

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


## Querying Linked Open Data 

For this tutorial, querying linked open data through a [SPARQL](https://www.w3.org/TR/sparql11-query/) endpoint service is not readily possible, because as a standalone showcase, only a static webspace has been provided.

However, linked data files may still be queried using client software.

### Querying linked open data using JavaScript

### Querying linked open data in Python

### Querying linked open data in QGIS

