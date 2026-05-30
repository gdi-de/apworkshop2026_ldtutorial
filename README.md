# Tutorial Linked Open Data AP Workshop 2026
 
This tutorial showcases the conversion of two Geodata sets retrieved from German geospatial agency websites into RDF (Linked Open Data) and how to handle the resulting data in practice.  

## Use Case 1: Baumkataster Hamburg (cadastre of trees alongside a street)
Trees in Germany are numbered and classified according to the type of tree, their position within an urban environment, and, finally, certain parameters that help characterize their shape.
 
[Use Case 1](Baumkataster.md) examines how to convert these parameters from a [tree cadastre in Hamburg](https://metaver.de/trefferanzeige?docuuid=D3FA796F-3D12-4784-B7F2-E19855472D2A) into Linked Open Data.

## Use Case 2: Trinkwasserbrunnen Berlin (drinking water fountains)  
   
Drinking water fountains are a common occurrence in many German cities, including Berlin. 
 
[Use Case 2](Trinkwasserbrunnen.md) investigates how a [dataset of drinking water fountains in Berlin](https://gdi.berlin.de/geonetwork/srv/api/records/8aaca41b-d665-447a-93ec-b76e431852fc) classified by type, origin, accessibility and location can be mapped to RDF.
 
   
## Publishing newly defined vocabularies   

During the dataset mapping process, a few terms could not be mapped to existing vocabularies.
However, their integration into the linked data deployments seemed important enough not to drop them.

For these terms, a new vocabulary definition needs to be created.

> [!IMPORTANT]
> A vocabulary will be represented as an RDF file, which only includes:
> - [rdfs:Class](http://www.w3.org/2000/01/rdf-schema#Class) or [owl:Class](http://www.w3.org/2002/07/owl#Class) instances with at least one label and one definition
> - [owl:DataTypeProperty](http://www.w3.org/2002/07/owl#DataTypeProperty), [owl:ObjectProperty](http://www.w3.org/2002/07/owl#ObjectProperty) or [owl:AnnotationProperty](http://www.w3.org/2002/07/owl#AnnotationProperty) instances with at least one label and one definition
> - Constraints on the aforementioned instances using [OWL](https://www.w3.org/TR/owl-features/) restrictions or [SHACL](https://www.w3.org/TR/shacl/) shapes

Generally, all published vocabularies should adhere to the 
[Best Practices for Implementing FAIR Vocabularies and Ontologies on the Web](https://dgarijo.com/papers/best_practices2020.pdf)

For the sake of this example, we will discuss only selected details of how these principles are implemented here.

> [!IMPORTANT]
> Important aspects when publishing a linked open data vocabulary:
> - Choosing a non-common prefix for the vocabulary. Check existing prefixes, e.g., on [prefix.cc](http://prefix.cc) and mark this prefix with a description in the [VANN vocabulary for annotating vocabulary descriptions](https://vocab.org/vann/)
> - Choose a vocabulary name that represents its contents
> - Choose an acronym for your vocabulary that is easy to remember

We then use the software [pyLODE](https://github.com/rdflib/pyLODE) to render the generated vocabulary on an HTML page and to make it dereferencable for human users.

In both datasets, we have defined a vocabulary namespace of [https://github.com/gdi-de/apworkshop2026_ldtutorial/ont#](https://gdi-de.github.io/apworkshop2026_ldtutorial/ont#) und which the vocabulary definition is deployed.

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
> - Which community of users do we anticipate will use this linked open data set?
> - Which data formats/data services do people in these communities commonly use?
> - Do we need to consider special laws and/or regulations when publishing linked data for this community?

For the two datasets, we cannot pinpoint a specific user base beyond people interested in using geodata.
There are likely use cases in administration and beyond, but for the sake of this tutorial, we cannot make a more precise assessment.

We therefore answer the aforementioned questions for a community of users working with geospatial data.

Typical GIS users are not necessarily familiar with the linked open data paradigm, but are familiar with the following forms of publication:
- Geospatial data formats (e.g., [GeoJSON](https://datatracker.ietf.org/doc/html/rfc7946))
- Geospatial APIs, e.g., [OGC API Features](https://ogcapi.ogc.org/features/) 

### Exposing data through (static) APIs

For all selected target communities, the selected APIs need to be investigated for their potential to provide the data that has been created.
Previously, we decided to provide additional support for GIS users by offering an OGC API Features service.

> [!IMPORTANT]
> Key questions:
> - How should the service be provided? Do we need an additional web application?
> - Which parts of the newly created linked open data graph should be exposed through the API, and how do we mark them in the knowledge graph?

In this tutorial, we rely solely on GitHub Pages for this repository, so a fully-fledged OGC API Features web service is out of scope.
Instead, we can create a static OGC API Features version that allows downloading full datasets but cannot provide search functionality.

> [!NOTE]
> We publish geospatial features as a single GeoJSON file, with one feature per instance.
> We infer groupings by subclass categorization and create collection instances in the knowledge graph.
> We then publish these collections as a static [OGC API Features](https://gdi-de.github.io/apworkshop2026_ldtutorial/collections/indexc.html) deployment so that the geodata, modeled as linked open data, can be accessed in GIS applications. We publish an [OpenAPI description](https://gdi-de.github.io/apworkshop2026_ldtutorial/api/api.html) of the static service.

> [!CAUTION]
> A static OGC API Features deployment will answer API calls to:
> - /landingpage
> - /capabilities
> - /collections
> - /collections/{COLLECTION}
> - /collections/{COLLECTION}/items
> - /collections/{COLLECTION}/items/{FEATUREID}
> 
However, the static version cannot handle API calls with HTML parameters such as "limit" and, especially, CQL queries.
> Hence, only complete feature collections are returned.
> For these additional capabilities, a real web service is required.

### Dereferencing URIs

URIs for instances in the dataset can be dereferenced using the previously mentioned deployments.

Take, for instance, the URI for one of the trees of the tree dataset:

```
https://gdi-de.github.io/apworkshop2026_ldtutorial/GZAW870
```
We expect this URI to resolve to at least one human-readable representation and one machine-readable representation in RDF.
Since we are working on GitHub Pages only, these serializations need to be added to the repository's web space, which can be explored in the gh-pages branch.

#### Content Negotiation

[HTTP Content Negotiation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Content_negotiation) 
is a negotiation process between a web browser and a web server to agree on the kind of format that will be submitted as the result of an HTTP request.
This means we could have a client-side request for an RDF serialization vs. another for HTML.

> [!CAUTION]
> Common HTTP web servers like [Apache](https://httpd.apache.org/) or [NGINX](https://nginx.org/) support content negotiation, however, not all publicly available webspaces support it.
> GitHub and GitLab pages have no support for Content Negotiation at this time.

Therefore, on GitHub pages webspaces, it is necessary to state the format by means of the URL itself, e.g., 

https://gdi-de.github.io/apworkshop2026_ldtutorial/GZAW870/index.html or 

https://gdi-de.github.io/apworkshop2026_ldtutorial/GZAW870/index.ttl

### Machine-readable linked data set description using VoID

The [Vocabulary of Interlinked Datasets (VoID)](https://www.w3.org/TR/void/) is a vocabulary to describe metadata about RDF datasets.
Compared to metadata such as DCAT, which describes context, licenses, and further information about the data publishing and serving process, VoID describes statistics, access metadata, and structural metadata, as well as the nature, structure, and links between multiple datasets.
Hence, data discovery within an RDF ecosystem is greatly enhanced.

> [!IMPORTANT]
> VoID provides many statistics that can be generated from the finished RDF Dump. Each statistic can help search engines to:
> - Index datasets better
> - Automatically categorize datasets better
> - Enable search engines, AI objects, and SPARQL resolvers to better find relevant data

**Example Triples:**
```ttl
@prefix void:<http://rdfs.org/ns/void#> .
@prefix xsd:<http://www.w3.org/2001/XMLSchema#> .
@prefix rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix dcterms: <http://purl.org/dc/terms/> .
ex:myds rdf:type void:Dataset ;
        rdfs:label "My dataset" ;
        void:exampleResource ex:myExampleResource ; # Specify a representative resource for the dataset
        void:rootResource ex:myRootResource ; # A resource from which the graph can be explored (fully) in an easy way: A well-connected node in the graph
        void:vocabulary geo: ; # Specify vocabularies used in the dataset
        void:dataDump "http://www.example.org/mydatadump.ttl"^^xsd:anyURI ; # Specify one ore more data dumps in different formats
        dcterms:subject <http://dbpedia.org/resource/Location>, <http://dbpedia.org/resource/OGC_GeoSPARQL ; # Defining themes of the dataset as URIs
        void:uriSpace "http://mydatanamespace" . # Definition of the data namespace in RDF

<ex:myds_subClassOf> a void:Dataset ;               # Property Partition: How often is a property used in the dataset?
    rdfs:label "Property Partition: subClassOf"@en ;
    void:property rdfs:subClassOf ;
    void:triples 10 .
```

> [!NOTE]
> **CHOICE:** We choose to add a VOID description to the dataset in order to make it better findable and reusable.

#### Defining used vocabularies explicitly using VOAF

The [Vocabulary of a Friend (VOAF)](http://lov.okfn.org/vocommons/voaf/) allows for describing vocabularies that are used in RDF graphs and can be used in conjunction with VoID to uniquely describe vocabulary contexts.

```ttl
@prefix voaf:<http://purl.org/vocommons/voaf#> .
@prefix rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix vann: <http://purl.org/vocab/vann/> .
@prefix geo:<http://www.opengis.net/ont/geosparql#> .
@prefix ex:<http://example.org/> .
geo: rdf:type voaf:Vocabulary ;
    rdfs:label "The GeoSPARQL Ontology"@en ;
    vann:preferredNamespacePrefix "geo"^^xsd:string ;
    vann:preferredNamespaceUri "http://www.opengis.net/ont/geosparql#"^^xsd:anyURI ;
    voaf:usageInDataset ex:myds .
```
We define the namespace geo: of GeoSPARQL as a [voaf:Vocabulary](http://lov.okfn.org/vocommons/voaf/Vocabulary), add statement that our datadump uses the GeoSPARQL vocabulary using   [voaf:usageInDataset](http://lov.okfn.org/vocommons/voaf/useageInDataset) and using [void:vocabulary](https://www.w3.org/TR/void/vocabulary) to point to the vocabulary usage from the [void:Dataset](https://www.w3.org/TR/void/Dataset) definition. 

In addition, we use the [Vocabulary Annotation Vocabulary (VANN)](http://purl.org/vocab/vann/) to define the preferred prefix for the vocabulary in our data dump, as well as the namespace URI of the vocabulary for further reference.

> [!NOTE]
> **CHOICE:** We choose to give a precise definition of the vocabulary we use in the dataset using the VOAF vocabulary.

#### Even more statistics with VoID Ext (VEXT)

[VoID Ext](https://www.ldf.fi/service/pylode?url=http://ldf.fi/void-ext) is an extension vocabulary to VoID that allows for modeling more precise statistics about the RDF dataset.

Additional statistics include:

- Namespace partitions
- IRI length partitions
- Datatype partitions
- Distinct Node counts

**Example Triples:** Numbers provided are fictional
```ttl
@prefix rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix void:<http://rdfs.org/ns/void#> .
@prefix vext:<http://ldf.fi/void-ext> .
@prefix ex:<http://example.org/> .
ex:myds rdf:type void:Dataset ;
    vext:averageLiteralLength 0 ;
    vext:averageObjectIRILength 39 ;
    vext:averagePropertyIRILength 38 ;
    vext:averageSubjectIRILength 55 ;
    vext:datatypes 0 ;
    vext:distinctBlankNodes 0 ;
    vext:distinctIRIReferences 44234 ;
    vext:distinctLiterals 0 ;
    vext:distinctRDFNodes 44234 ;
    vext:languages 0 ;
    vext:propertyClasses 0 .
```

> [!NOTE]
> **CHOICE:** We choose to include VEXT to maximize the reusability of the data dump.


## Data Discovery: Visualizing RDF/OWL class structures

When an RDF file has been created and published it is important that the data and the data structure within the dataset can be explored and understood by humans.
Upon discovery of a dataset, when it comes to its actual contents, humans often want to answer one of the following questions:

> [!IMPORTANT]
> - Are the contents of the dataset relevant to my application case?
> - Which data fields are included in the dataset?
> - What is the method of classification applied to the data?
> - How interoperable and detailed is this dataset compared to the data I work with usually?

To that end, the classifications applied in the mapping process to RDF must be made transparent to the end user and visualized in a human-readable manner.
In the following, we introduce two approaches to RDF visualizations as an example. Both approaches are applied in the data deployment.

### Using the Visual Notation For OWL Ontologies (VOWL)

The [Visual Notation for OWL Ontologies (VOWL)](https://www.semantic-web-journal.net/system/files/swj1114.pdf) is a JSON notation, which allows a JavaScript-based viewer to display essential components of a vocabulary:
- Classes and Properties
- Ranges and Domains
- Constraint relations between the aforementioned items

A demo site of VOWL is available [here](https://service.tib.eu/webvowl/).

VOWL allows users to gain insights into the classifications applied to a dataset without having to examine its data instances.
Users may, at a glance, be allowed to get an impression of what content is available in the dataset to answer questions like:
- Is that dataset worth investigating further?
- What is the definition of the classifications, and is the definition aligned with my goals?

> [!NOTE]
> **CHOICE:** We choose to add a VOWL description of all relevant classes to the homepage of the HTML deployment.

### Representing class hierarchies with CT

The [classtree vocabulary CT](https://sparqlunicorn.github.io/sparqlunicornGoesGIS-ontdoc/classtreevocab.html) allows to annotate classtree hierarchies with specific classifiers, so that class tree visualizations can be derived in a better way.

The CT vocabulary defines tree nodes that categorize specific classes by the contents they link to.

For instance, it adds whether a class links to geodata, whether a property should link to sth. with a unit or to sth. like a concept of time.

This allows rendering software to display this information in a tree visualization, such as in this HTML deployment.

<img width="339" height="410" alt="image" src="https://github.com/user-attachments/assets/10461060-75cc-4f53-a36c-78481dec0ad4" />

In this excerpt from a class tree visualization of this repository, we can see general classes displayed as a yellow circle.

Classes that are known to have geometry instances in the dataset are displayed with an icon representing planet Earth.

In this way, users obtain more visual information about classes, which might help them decide on further discovery.

<img width="516" height="713" alt="image" src="https://github.com/user-attachments/assets/41542418-6704-4e12-9e47-37a3e0d9a0d4" />

An investigation of one of the geoclasses using the context menu can tell us which properties are associated with the instance.

We can observe a property which is typically linked to an instance of a geometry ("hasGeometry") marked up using the earth icon or the rdfs:label property indicating the label marked up with the icon for a property associated with labels.

> [!NOTE]
> **CHOICE:** We choose to add a classtree view based on the principles of the classtree vocabulary to the HTML deployment in order to simplify the navigation and classification of data instances and classes for users

### Using third-party graph visualizing software

Software to visualize graph structures has been developed since many years, but they are rarely readily available to parse RDF.
To be able to view RDF graphs, in graph visualization software, RDF therefore has to be converted to other formats such as:

- [GraphML](http://graphml.graphdrawing.org/specification.html)
- [GEXF](https://gexf.net/)

Typical software to use would be:
- [yEd](https://www.yworks.com/products/yed)
- [Gephi](https://gephi.org/)

In this tutorial, no graph format export has been configured to the HTML deployment, but it could just be another serialization to be considered.

> [!NOTE]
> **CHOICE:** In this tutorial, we do not provide an export for third-party graph visualization software.

## Findability of linked data

Since linked open data is also published in HTML, it can be convenient for search engines to index it, provided the HTML is properly formatted for this purpose.
We describe three technologies that aid search engines in indexing published linked open data pages and explain how they were applied in this tutorial.

### RDFa

[RDF in Attributes](https://www.w3.org/TR/rdfa-core/) ([RDFa](https://rdfa.info/)) is a way to embed RDF in HTML webpages.
The goal is to use existing HTML elements on a homepage and mark them up so that their contents can be interpreted in RDF.
The HTML homepage, thereby acts as the subject of the RDF triple.
Elements are marked up with a predicate URI and use their value as the object.

```html
<div vocab="http://schema.org/" typeof="Person">
  <a property="image" href="http://manu.sporny.org/images/manu.png">
    <span property="name">Manu Sporny</span></a>, 
  <span property="jobTitle">Founder/CEO</span>
  <div>
    Phone: <span property="telephone">(540) 961-4469</span>
  </div>
  <div>
    E-mail: <a property="email" href="mailto:msporny@digitalbazaar.com">msporny@digitalbazaar.com</a>
  </div>
  <div>
    Links: <a property="url" href="http://manu.sporny.org/">Manu's homepage</a>
  </div>
</div>
```
<img width="705" height="420" alt="image" src="https://github.com/user-attachments/assets/f716a081-39c8-4bef-8252-aa19aa3849ad" />

The example, as taken from the [RDFa Playground](https://rdfa.info/play/) shows how a simple homepage in which elements of a person are described can be marked up in RDF.
The resulting graph can be queried using the schema.org vocabulary.

### HTML Microdata

[HTML Microdata](https://www.w3.org/TR/2021/NOTE-microdata-20210128/) is another competing format to RDFa, which marks up elements of a homepage.
The syntax is simpler and is expressivity is not as developed as RDFa.

```html
<div itemscope itemtype="http://schema.org/Person"> 
	Hello, my name is 
	<span itemprop="name">John Doe</span>, 
	I am a 
	<span itemprop="jobTitle">graduate research assistant</span> 
	at the 
	<span itemprop="affiliation">University of Dreams</span>. 
	My friends call me 
	<span itemprop="additionalName">Johnny</span>. 
	You can visit my homepage at 
	<a href="http://www.example.com/~JohnnyD" itemprop="url">www.example.com/~JohnnyD</a>. 
	<div itemprop="address" itemscope itemtype="http://schema.org/PostalAddress">
		I live at 
		<span itemprop="streetAddress">1234 Peach Drive</span>,
		<span itemprop="addressLocality">Warner Robins</span>,
		<span itemprop="addressRegion">Georgia</span>.
	</div>
</div>
```

See [this article](https://medium.com/@davetekle/microdata-vs-microformats-vs-rdfa-a-guide-to-structured-data-on-the-web-8fff03a48cfd) for a brief comparison of the two standards.

### JSON-LD

The last option is to include [JSON-LD](https://www.w3.org/TR/json-ld11/) natively in HTML.
Using this method, the expressivity is the same as RDFa, but the RDFa annotation is at a specific place in the HTML document.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Hummelsen",
    "addressRegion": "NRW",
    "streetAddress": "Op de Dyk"
  },
  "description": "Bestens erhaltene und aufgearbeitete Möbel im Landhausstil.",
  "name": "Vintage Möbel und Deko am Dyk",
  "telephone": "850-648-4200"
}
</script>
```

JSON-LD is embedded in a script tag of type application/ld+json in HTML which contains all metadata which would be encoded in other microformats.

### Application in the LOD publication

The HTML deployment in this example takes advantage of all three methods.
The generated HTML has both RDFa and Microdata attached.
In addition, a JSON-LD version of each linked data published site is available in the deployment.

In this way, JSON-LD is not embedded in the HTML page, as the JSON-LD markup suggests, but a search engine will be able to pick up the JSON-LD serialization in the same publication folder.

To explore Microdata published in this way, users may install extensions such as the [Virtuoso Structured Data Sniffer](https://chromewebstore.google.com/detail/openlink-structured-data/egdaiaihbdoiibopledjahjaihbmjhdj?pli=1), a browser extension that visualizes all aformentioned Microformats.

> [!NOTE]
> **CHOICE:** Becauses of the capabilities of the software we use for HTML deployment, little effort is needed to create a deployment that fulfills all of the aforementioned formats.
> Hence we choose to support them all simultaneously. In a different setting either RDFa or JSON-LD might suffice.

## Querying Linked Open Data 

For this tutorial, querying linked open data through a [SPARQL](https://www.w3.org/TR/sparql11-query/) endpoint is not readily possible because, as a standalone showcase, only a static web space has been provided.

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
g.parse("https://gdi-de.github.io/apworkshop2026_ldtutorial/index.ttl")

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

Support for linked open data resources in [QGIS](https://qgis.org/) is given by the [SPARQLing Unicorn QGIS plugin](https://github.com/sparqlunicorn/sparqlunicornGoesGIS).

The plugin may be used to include the aggregated data dump at the following address:

```
https://gdi-de.github.io/apworkshop2026_ldtutorial/index.ttl
```

The data dump will be downloaded in QGIS and further processed in the plugin using RDFLib.
The query capabilities of this data dump will therefore depend on the query capabilities of the RDFLib library.

## Publication Workflow

The publication workflow as used in this repository is illustrated in [this section](Workflow.md)
