# Mapping-OWLtoOAS

Generate an OpenAPI Specification (OAS) from OWL.

**Authors:** Paola Espinoza-Arias and Daniel Garijo

**Versions:** 1.0.0

**Release date:** 18/05/2020

**License:** [License (Apache-2.0)](https://github.com/paoespinozarias/Mapping-OWLtoOAS/blob/master/LICENSE)

## Introduction

This is a document defining how to translate [OWL](https://www.w3.org/TR/owl2-overview/) to [OAS](https://github.com/OAI/OpenAPI-Specification) to allow developers to figure out how ontologies may be represented as an interface description for REST APIs. Here you will find the correspondences between both representations and simple examples of how an ontology code looks like in the OAS.



## Table of Contents

- [Definitions from OAS](#definitions)
- [Mapping between OWL and OAS](#mapping)
  - [Class](#class)
  - [Data Types](#dataTypes)
  - [Properties](#properties)
  - [Restrictions](#restrictions)
  - [Operations](#operations)
  - [Ontology Metadata](#ontologyMetadata)
  - [Limitations](#limitations)



### <a name="definitions"></a>Definitions from OAS

The following definitions have been taken from the [OAS version 3.0.3](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md) as a reduced version of all the details provided in such specification. Only those definitions that will be used in the mapping section are briefly presented.

It is worth nothing that the remaining definitions provided in OAS should be followed in the same manner as are shown in the specification.

#### <a name="componentsObject"></a>Components Object

Holds a set of reusable objects for different aspects of the OAS.
All objects defined within the [Components Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#componentsObject) will have no effect on the API unless they are explicitly referenced from properties outside the components object.


#### <a name="pathsObject"></a>Paths Object

Holds the relative paths to the individual endpoints and their operations.
The [path](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#pathsObject) is appended to the URL from the [Server Object](#https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#serverObject) in order to construct the full URL.  

#### <a name="pathItemObject"></a>Path Item Object

Describes the operations available on a single path (get, put, post, among others). The path itself is still exposed to the documentation viewer but they will not know which operations and parameters are available.
To check more details provided in the OAS refer to [Path Item Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#paths-object.
).

#### <a name="referenceObject"></a>Reference Object

A simple object to allow referencing other components in the specification, internally and externally. The [Reference Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#referenceObject) is defined by [JSON Reference](https://tools.ietf.org/html/draft-pbryan-zyp-json-ref-03) and follows the same structure, behavior and rules. For OAS, reference resolution is accomplished as defined by the JSON Reference specification and not by the JSON Schema specification.

#### <a name="schemaObject"></a>Schema Object

The [Schema Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#schemaObject) allows the definition of input and output data types. These types can be objects, but also primitives and arrays. This object has several properties taken directly from the JSON shema definition, such as title, required, maximum, among others. In addition, this object manages other properies taken from the JSON Schema definition but their definitions were adjusted to the OpenAPI Specification, such as type, allOf, oneOf, etc.

Alternatively, any time a Schema Object can be used, a [Reference Object](#referenceObject) can be used in its place. This allows referencing definitions instead of defining them inline. Other than the JSON Schema subset fields, the following fields MAY be used for further schema documentation: nullable, deprecated, readOnly, etc.

#### <a name="dataTypes"></a>Data Types

Primitive [data types](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#dataTypes) in the OAS are based on the types supported by the [JSON Schema Specification Wright Draft 00](https://tools.ietf.org/html/draft-wright-json-schema-00#section-4.2). In addition, primitives have an optional modifier property: `format`. OAS uses several known formats to define in fine detail the data type being used. However, to support documentation needs, the `format` property is an open `string`-valued property, and can have any value. Formats such as "email", "uuid", and so on, MAY be used even though undefined by this specification. Types that are not accompanied by a format property follow the type definition in the JSON Schema. Tools that do not recognize a specific format MAY default back to the type alone, as if the format is not specified.


## <a name="mapping"></a>Mapping between OWL and OAS

In this section the correspondences between [OWL](https://www.w3.org/TR/owl2-quick-reference/) and OAS are provided. All mappings include examples provided in [Turtle](https://www.w3.org/TR/turtle/) and [YAML](https://yaml.org/) serializations for human-friendly readability. The code of both serializations together with an ontology diagram are provided in [examples](examples). Also, the full example in YAML serialization may be opened in the [Swagger editor](http://editor.swagger.io) to check it as an API documentation.

The prefixes that will be used in this section are:

```prefix
@prefix : <https://w3id.org/example#> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix xml: <http://www.w3.org/XML/1998/namespace> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix prov: <http://www.w3.org/ns/prov#> .
```

#### <a name="class"></a>Class


[`OWL`](#owl) | [`OAS`](#oas) | Comments
------ | -------- | --------
`owl:Class` | `Schema Object` | It should be defined as a [Schema Object](#schemaObject) in the [Component Object](#componentsObject) definition. The `Schema Object` must be a`type: object`. The schema name should be the `rdfs:label` value defined in the OWL Class. It is worth noting that such label should not contain blank spaces. [See example](#classexample)
**Class descriptions** |
`owl:intersectionOf` | `allOf` | It should be defined as `allOf` which validates the value against all the subschemas.
`owl:unionOf` | `anyOf` | It should be defined as `anyOf` which validates the value against any (one or more) the subschemas. |
`owl:complementOf` | `not` | It should be defined as `not` which validates the value is not valid against the specified schema |
`owl:oneOf` | `enum` | It should be defined as an `enum` and the values included in this enumeration list. |
**Class axioms** |
`rdfs:subClassOf` | `allOf`| It should be defined as a model composition of the common property set and class-specific. Thus, a subclass should be defined as a [Schema Object](#schemaObject) in the [Component Object](#componentsObject) definition including the field `allOf`. In such field should also be defined a `type: object` and the corresponding [Reference Object](#referenceObject) (`$ref`:'reference to the Parent Class'). Finally, in the `properties` field should be defined the own subclass properties. [See example](#subclassexample)|
`owl:equivalentClass` | not covered |
`owl:disjointWith` | not covered |
`owl:AllDisjointClasses` | not covered |

##### <a name="classexample"></a>Class definition example

This example shows the definition of the Person class. However, its properties have been omitted because the details of the Data and Object properties definition will be provided in the next section.

TTL
```ttl
:Person rdf:type owl:Class ;
rdfs:label "Person"@en.
```
YAML
```yaml
components:
  schemas:
    Person:
      type: object
```
[Back to the Class mapping](#class)

##### <a name="subclassexample"></a>Subclass definition example

This example shows the definition of the Professor and Student subclasses. However, the specific properties of each subclass have been omitted because details on how to define Data and Object properties will be provided in the following sections.

TTL
```ttl
:Professor rdf:type owl:Class ;
    rdfs:subClassOf :Person ,
    owl:disjointWith :Student ;
    rdfs:label "Professor"@en .

:Student rdf:type owl:Class ;
    rdfs:subClassOf :Person ,
    owl:disjointWith :Professor ;
    rdfs:label "Student"@en .

```
YAML
```yaml
components:
  schemas:
    Person:
      type: object
      Professor:
          allOf:
          - $ref: '#/components/schemas/Person'
          - type: object
      Student:
          allOf:
          - $ref: '#/components/schemas/Person'
          - type: object
```
[Back to the Class mapping](#class)

#### <a name="dataTypes"></a>Data Types

In the following, the correspondence between both data types is shown:

OWL |OAS | Comments
------ | -------- | --------
`xsd:integer`| `integer` |
 `xsd:int`| `integer` | The `format` must be defined as `int32` (signed 32 bits)
 `xsd:long`| `integer` | The `format` must be defined as `int64` (signed 64 bits)
 `xsd:float`| `number` | The `format` must be defined as `float` |
 `xsd:double`| `number` |The `format` must be defined as `double` |
 `xsd:string`|`string` |
 `xsd:dateTime`|`string` | The `format` must be defined as `date` (As defined by `full-date` - [RFC3339](https://xml2rfc.ietf.org/public/rfc/html/rfc3339.html#anchor14))
 `xsd:dateTimeStamp`|`string` | The `format` must be defined as `date-time` (As defined by `date-time` - [RFC3339](https://xml2rfc.ietf.org/public/rfc/html/rfc3339.html#anchor14))
 `xsd:byte`|`string` | The `format` must be defined as `byte` (base64 encoded characters)
  `xsd:boolean`|`boolean` |


#### <a name="properties"></a>Properties

[`OWL`](#owl) | [`OAS`](#oas) | Comments
------ | -------- | --------
`owl:DatatypeProperty` | `properties` | It should be defined as a property of the corresponding [Schema Object](#schemaObject) instance, as aforementioned in the `owl:Class` mapping. The property name will be the `rdfs:label` value defined in the OWL DatatypeProperty. It is worth noting that such label should not contain blank spaces.
`owl:ObjectProperty` | `properties` | It should be defined as a property of the corresponding [Schema Object](#schemaObject) instance, as aforementioned in the `owl:Class` mapping. The property name will be the `rdfs:label` value defined in the OWL ObjectProperty. It is worth noting that such label should not contain blank spaces.
**Property axioms** |
`rdfs:domain` | `Schema Object`| It should be defined as a [Schema Object](#schemaObject) inside of the [Component Object](#componentsObject) definition. The `Schema Object` must be a `type: object` with a [Reference Object](#referenceObject) (`$ref:`'reference to the Domain Class') . |
`rdfs:range` | `type`| **_a)_** when the range corresponds to an `owl:DatatypeProperty`: if the range is single-valued it should be defined according to its [data type](dataTypes); else the range should be defined compliant to its [data type](dataTypes) and the possible values defined as an enumeration (`enum`). **_b)_** when the range corresponds to an `owl:ObjectProperty`: if the range cardinality >=1 it should be defined as an `array` type and the `items` as a [Reference Object](#referenceObject) (`$ref:`'reference to the Range Class'); else if the range cardinality <1 it could be defined as an `array` type and `items` as a [Reference Object](#referenceObject) (`$ref:`'reference to the single Range Class') or only as an `object` type. This last decision will depend on the developer decision. |
`rdfs:subPropertyOf` | not covered |
`owl:equivalentProperty` | not covered |
`owl:propertyDisjointWith` | not covered |
`owl:AllDisjointProperties` | not covered |
`owl:inverseOf` | not covered |
**Property characteristics** |
`owl:FunctionalProperty` | `maxItems`| The property with this characteristic should be defined as an array (`type: array`) with 1 as the maximum number of items (`maxItems:` 1) and **_a)_** if it corresponds to an `owl:DatatypeProperty` the type of array items must be the [data type](dataTypes) range; **_b)_** if it corresponds to an `owl:ObjectProperty` the type of array items must contain a [Reference Object](#referenceObject) to the range Class (`$ref:`'reference to the range Class'). |
`owl:TransitiveProperty` | not covered |
`owl:SymmetricProperty` | not covered |
`owl:AsymmetricProperty` | not covered |
`owl:InverseFunctionalProperty` | not covered |
`owl:ReflexiveProperty` | not covered |
`owl:IrreflexiveProperty` | not covered |

**Data Property definition example**

The following example presents how to define a Data Property of the Person class. A single-value and a multi-value ranges has been included to show their definition in OAS.

TTL
```ttl
:identifier rdf:type owl:DatatypeProperty , owl:FunctionalProperty ;
  rdfs:domain :Person ;
  rdfs:range xsd:string ;
  rdfs:label "identifier"@en .

:gender rdf:type owl:DatatypeProperty ;
  rdfs:domain :Person ;
  rdfs:range [ rdf:type rdfs:Datatype ;
    owl:oneOf [ rdf:type rdf:List ;
      rdf:first "female" ;
      rdf:rest [ rdf:type rdf:List ;
        rdf:first "male" ;
        rdf:rest rdf:nil
        ]
      ]
    ] ;
  rdfs:label "gender"@en .

```
YAML
```yaml
components:
  schemas:
    Person:
      type: object
      properties:
        identifier:
          items:
            type: string
            nullable: false
          type: array
          maxItems: 1
        gender:
          type: string
          enum:
            - male
            - female
          nullable: true
```

The next example shows how to define a Data Property of the Professor subclass:

TTL
```ttl
:researchField rdf:type owl:DatatypeProperty ;
    rdfs:domain :Professor ;
    rdfs:range xsd:string ;
    rdfs:label "research field"@en .
```
YAML
```yaml
components:
  schemas:
    Person:
      type: object
      properties:
        identifier:
          items:
            type: string
            nullable: false
          type: array
          maxItems: 1
        gender:
          type: string
          enum:
            - male
            - female
          nullable: true
    Professor:
      allOf:
        - $ref: '#/components/schemas/Person'
        - type: object
      properties:
        researchField:
          type: string
          nullable: true
```
[Back to the Properties mapping](#properties)

**ObjectProperty definition example**

The following example shows how to define an Object Property of the Professor class:

TTL

```ttl
:teachesTo rdf:type owl:ObjectProperty ;
    rdfs:domain :Professor ;
    rdfs:range :Student ;
    rdfs:label "teaches to"@en .
```
YAML
```yaml
components:
  schemas:
    Person:
      type: object
      properties:
        identifier:
          items:
            type: string
            nullable: false
          type: array
          maxItems: 1
        gender:
          type: string
          enum:
            - male
            - female
          nullable: true
    Professor:
      allOf:
        - $ref: '#/components/schemas/Person'
        - type: object
      properties:
        researchField:
          type: string
          nullable: true
        teachesTo:
          items:
            $ref: '#/components/schemas/Student'
          type: array
          nullable: true
```
The next example shows how to define an Object Property of the Student class. Note that this is a Functional Property.

TTL

```ttl
:hasRecord rdf:type owl:ObjectProperty , owl:FunctionalProperty ;
    rdfs:domain :Student ;
    rdfs:range :StudentRecord ;
    rdfs:label "has record"@en .
```
YAML
```yaml
components:
  schemas:
    Person:
      type: object
      properties:
        identifier:
          items:
            type: string
            nullable: false
          type: array
          maxItems: 1
        gender:
          type: string
          enum:
            - male
            - female
          nullable: true
    Student:
      allOf:
        - $ref: '#/components/schemas/Person'
        - type: object
      properties:
        hasRecord:
          items:
            $ref: '#/components/schemas/StudentRecord'
          type: array
          maxItems: 1
          nullable: true
```
[Back to the Properties mapping](#properties)

#### <a name="restrictions"></a>Restrictions

[`OWL`](#owl) | [`OAS`](#oas) | Comments
------ | -------- | --------
`owl:onProperty` |  `properties` | It should refers to the property name where the restriction is applied.
`owl:onClass` | `Schema Object` | It should refers to the schema name where the restriction is applied.
`owl:someValuesFrom`| `type`| The restricted property should be defined as an array (`type: array`) and specified as `required`.  In addition, the following details should be adopted: **_a)_** when the restriction corresponds to an `owl:DatatypeProperty`the items type should be defined according to the restricted [data type](dataTypes); **_b)_** when the restriction corresponds to an `owl:ObjectProperty` the `items` should be a [Reference Object](#referenceObject) to the restricted class (`$ref:`'reference to the restricted Class'). [See example](#someValuesFromExample) |  
`owl:allValuesFrom` | `type` | The restricted property should be defined as an array (`type: array`) according to the following details: **_a)_** when the restriction corresponds to an `owl:DatatypeProperty` the items type should be defined according to the restricted data type; **_b)_** when the restriction corresponds to an `owl:ObjectProperty` the items should be a Reference Object to the restricted class (`$ref`:'reference to the restricted Class'). [See example](#allValuesFromExample)|
`owl:hasValue`| `type` `default` | **_a)_** when corresponds to a literal value of an `owl:DatatypeProperty`  **_b)_** when the range corresponds to an individual value of an `owl:ObjectProperty` it should be represented as a [Reference Object](#referenceObject) to the individual URI value (`$ref:`'individual URI value'). [See example](#hasValueExample)|
`owl:hasValue`| `default` | It should be defined as a default value of the property. Depending on the restriction  **_a)_** when it is on an `owl:DatatypeProperty` the property `type` should be defined as the corresponding [data type](dataTypes) and the default value should be the specific literal value, **_b)_** when it is on an `owl:ObjectProperty` the property type should be `type: string` with `format: uri` and the default value should be the individual URI value. |
`owl:minCardinality` | `minItems` | It should be defined as the minimum number of the array items which contains as [Reference Object](#referenceObject) a scheme of the `owl:Thing` (`$ref:`'reference to the owl:Thing'). The value of `minItems` must be a non-negative number. |
`owl:maxCardinality` | `maxItems`| It should be defined as the maximum number of the array items which contains as [Reference Object](#referenceObject) a scheme of the `owl:Thing` (`$ref:`'reference to the owl:Thing'). The value of `maxItems` must be a non-negative number.|
`owl:cardinality` | `minItems` and `maxItems` | It should be defined as the same minimum and maximum number of the array items which contains as [Reference Object](#referenceObject) a scheme of the `owl:Thing` (`$ref:`'reference to the owl:Thing'). The value of `minItems` and `maxItems` must be a non-negative number. |
`owl:minQualifiedCardinality` | `minItems`| It should be defined as the minimum number of array items and **_a)_** if it corresponds to an `owl:DatatypeProperty` the type of array items must be the restricted [data type](dataTypes); **_b)_** if it corresponds to an `owl:ObjectProperty` the type of array items must contain the [Reference Object](#referenceObject) to the restricted Class (`$ref:`'reference to the restricted Class'). The value of `minItems` must be a non-negative number.|
`owl:maxQualifiedCardinality` |`maxItems`| It should be defined as the maximum number of array items and **_a)_** if it corresponds to an `owl:DatatypeProperty` the type of array items must be the restricted [data type](dataTypes); **_b)_** if it corresponds to an `owl:ObjectProperty` the type of array items must contain the [Reference Object](#referenceObject) to the restricted Class (`$ref:`'reference to the restricted Class'). The value of `maxItems` must be a non-negative number.|
`owl:qualifiedCardinality`|`minItems` and `maxItems` |  It should be defined as the same minimum and maximum number of the array items which contains **_a)_** if it corresponds to an `owl:DatatypeProperty` the restricted [data type](dataTypes); **_b)_** if it corresponds to an `owl:ObjectProperty` the [Reference Object](#referenceObject) to the restricted Class (`$ref:`'reference to the restricted Class'). The value of `minItems` and `maxItems` must be a non-negative number. |

##### <a name="restrictionsExamples"></a>Examples

In the following, several examples for each restriction covered by the mapping are provided. Note that only properties affected by the restriction are shown. In some cases dots are included in the YAML example as an signal that some values are omitted e.g. Person: ....

##### <a name="someValuesFromexample"></a>someValuesFrom example


TTL
```ttl
:Professor rdf:type owl:Class ;
  rdfs:subClassOf :Person ,
    [ rdf:type owl:Restriction ;
      owl:onProperty :teachesCourse ;
      owl:someValuesFrom :Course
    ] .
```
YAML
```yaml
components:
  schemas:
    Person: ...
    Professor:
      allOf:
        - $ref: '#/components/schemas/Person'
        - type: object
      properties:
        teachesCourse:
          items:
            $ref: '#/components/schemas/Course'
          type: array
      required:
        - teachesCourse
```
[Back to the Restrictions mapping](#restrictions)

##### <a name="allValuesFromExample"></a>allValuesFrom example
TTL
```ttl
:Professor rdf:type owl:Class ;
  rdfs:subClassOf :Person ,
    [ rdf:type owl:Restriction ;
      owl:onProperty :teachesTo ;
      owl:allValuesFrom :Student
    ] .
```
YAML
```yaml
components:
  schemas:
    Person: ...
    Professor:
      allOf:
        - $ref: '#/components/schemas/Person'
        - type: object
      properties:
        teachesTo:
          items:
            $ref: '#/components/schemas/Student'
          type: array
```
[Back to the Restrictions mapping](#restrictions)

##### <a name="hasValueExample"></a>hasValue example
TTL
```ttl
:Student rdf:type owl:Class ;
  rdfs:subClassOf :Student ,
    [ rdf:type owl:Restriction ;
      owl:onProperty :nationality ;
      owl:hasValue "American"
    ] .
```
YAML
```yaml
components:
  schemas:
    Student: ...
    AmericanStudent:
      allOf:
        - $ref: '#/components/schemas/Student'
        - type: object
      properties:
        nationality:
          type: string
          default: "American"
```
[Back to the Restrictions mapping](#restrictions)

**Restrictions example**

TTL
```ttl

:Professor rdf:type owl:Class ;
  rdfs:subClassOf :Person ,
    [ rdf:type owl:Restriction ;
      owl:onProperty :teachesTo ;
      owl:someValuesFrom :Student
    ] ,
    [ rdf:type owl:Restriction ;
      owl:onProperty :worksIn ;
      owl:allValuesFrom :University
    ] ,
    [ rdf:type owl:Restriction ;
      owl:onProperty :worksIn ;
      owl:minQualifiedCardinality "1"^^xsd:nonNegativeInteger ;
      owl:onClass :University
    ] ;
      owl:disjointWith :Student ;
      rdfs:label "Professor"@en .
```

YAML
```yaml
components:
  schemas:
    Person:
      type: object
      properties:
        identifier:
          items:
            type: string
            nullable: false
          type: array
          maxItems: 1
        gender:
          type: string
          enum:
            - male
            - female
          nullable: true
    Professor:
      allOf:
        - $ref: '#/components/schemas/Person'
        - type: object
      properties:
        researchField:
          type: string
          nullable: true
        teachesTo:
          items:
            $ref: '#/components/schemas/Student'
          type: array
        worksIn:
          items:
            $ref: '#/components/schemas/University'
          type: array
          maxItems: 1
          nullable: false
      required:
        - teachesTo
```
[Back to the Restrictions mapping](#restrictions)

#### <a name="pathsandoperations"></a>Paths and Operations

##### <a name="paths"></a>Paths

On the one hand, paths in OAS are resources that the API exposes. Thus, its correspondence from OWL are classes and will represented in the following manner:

OWL | OAS | Comments
---|:---:|---
`owl:Class`  | `/{path}`  | It should be defined as a `/{path}` (following the  [Path Item Object](#pathItemObject) format). The `{path}` value corresponds to the `rdfs:label` value defined in the OWL Class. Note that the label value need to be defined in plural and following a lowercase capitalization according to the naming practices in REST API. In addition if the label value is composed by several words they should be provided with a hyphen delimiter.

**Path example**

The following example corresponds to the path definition of the Professor class:

TTL
```ttl
:Professor rdf:type owl:Class ;
rdfs:label "Professor"@en.
```
YAML
```YAML
paths:
  /professors:
```
The next example shows the path definition of the StudyProgram class. Note that the naming conventions aforementioned have been followed:

TTL
```ttl
:StudyProgram rdf:type owl:Class ;
rdfs:label "Study Program"@en.
```
YAML
```YAML
paths:
  /study-programs:
```
The last part regarding to Paths is the [Path Templating](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#path-templating) which allows to the usage of template expressions in curly braces `{}` to mark a section of a URL path as replaceable using path parameters.

##### <a name="operations"></a>Operations

On the other hand, operations in OAS are the HTTP methods used to manipulate these paths. Such operations are defined in [Path Item Objects](#pathItemObject) as [Operation Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#operationObject). It is worth mentioning that a single path can support multiple operations but only one instance of them, for example it is allowed to define only one GET an only one POST for the same path.

In the following, only those elements where an OWL element needs to be mapped to OAS are presented:

OWL | OAS | Comments
---|:---:|---
`owl:Class`  | `schema`  | It should be defined as part of the [Response Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#response-object) and as a [Reference Object](#referenceObject) of the [Schema Object](#schemaObject).

In the following some basic examples about `get` operations are provided. For simplicity the response of the `200` HTTP status code is show together with the content defined as `application/json` media type. In OAS further details about all supported [HTTP codes](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#http-status-codes) and [Media Types](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#media-types) are provided.

**Operation examples**

This example shows the `get` operation retrieving all instances of a class. Note that only the Response Object is included; however, other fields not presented in this example may be added such as described in the [Operation Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#operation-object) defintion.  

```YAML
/classlabel:
  get:
    responses:
      '200'
      description: A message describing the result of the operation.
      content:
        application/json:
          schema:
            items:
              $ref: ‘#/components/schemas/ClassLabel’
            type: array
```
The next example shows the `get` operation retrieving an instance of a class by id. Note that in this case the response corresponds to a single instance of the class instead of an array of items of the class such was presented in the previous example.

```YAML
/classlabel/{id}:
  get:
    responses:
      '200'
      description: A message describing the result of the operation.
      content:
        application/json:      
          schema:
            $ref: ‘#/components/schemas/ClassLabel’
```
Finally, the following examples show the both previous operations for the Professor class:

```YAML
/professors:
  get:
    responses:
      '200':
        description: The list of Professor has been retrieved successfully
        content:
          application/json:
            schema:
              items:
                $ref: ‘#/components/schemas/Professor’
              type: array

/professors/{id}:
  get:
    responses:
      '200':
        description: The Professor has been retrieved successfully
        content:
          application/json:
            schema:
              $ref: ‘#/components/schemas/Professor’   
```

#### <a name="ontologyMetadata"></a>Ontology metadata

In the following, the mapping between the most common annotation properties describing OWL ontologies and OAS is presented. These properties has been retrieved from the [Best Practices for Implementing FAIR Vocabularies and Ontologies on the Web](https://arxiv.org/abs/2003.13084)

 [`OWL`](#owl) | [`OAS`](#oas) | Comments
 ------ | -------- | --------
`dcterms:title` |`title`| It should described with its value in the `title` field of [Info Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#infoObject).   
`rdfs:label` | _name_  | It should be applied in the [Schema Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#schemaObject) as the name of the corresponding class or property. Note that _name_ is not an OAS keyword for the `Schema Object`, but it is provided as a way to make sense to such correspondence.
`rdfs:comment`, `dcterms:description`, `prov:definition` |  `description` | It should be applied in the [Schema Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#schemaObject) as a string description of the corresponding class or property. |
`rdfs:seeAlso` |   |
`rdfs:isDefinedBy` |   |
`dcterms:source`||
`dcterms:published`||
`vaem:example`||



#### <a name="limitations"></a>Limitations

It is worth noting that complex representations are not supported by the mapping. In the following, some cases are listed:

- Multiple inheritance, e.g. ClassA `rdfs:subClassOf` (ClassB `owl:intersectionOf` ClassC).
- Complex range restrictions on a property such as:
  - The union of two intersections, e.g. ((ClassA `owl:intersectionOf` ClassB) `owl:unionOf` (ClassC `owl:intersectionOf` ClassD)).
