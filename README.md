# Mapping-OWLtoOAS

Generate an Open API Specification (OAS) from OWL.

This is a document defining how to translate OWL ....

**Authors:** Paola Espinoza-Arias, Daniel Garijo

**Versions:** 1.0.0

**Release date:** 15/05/2020

## Table of Contents

- [Definitions from OAS](#definitions)
- [Mapping between OWL and OAS](#mapping)
  - [Class](#class)
  - [Data Types](#dataTypes)
  - [Properties](#properties)
  - [Restrictions](#restrictions)
  - [Operations](#operations)
  - [Metadata](#metadata)



### <a name="definitions"></a>Definitions from OAS

The following definitions have been taken from the [OAS version 3.0.3](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md) as a reduced version of all the details provided in such specification. Only those definitions that will be used in the mapping section are presented.

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

Primitive [data types](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#dataTypes) in the OAS are based on the types supported by the [JSON Schema Specification Wright Draft 00](https://tools.ietf.org/html/draft-wright-json-schema-00#section-4.2). In addition, primitives have an optional modifier property: `format`. OAS uses several known formats to define in fine detail the data type being used. However, to support documentation needs, the `format` property is an open `string`-valued property, and can have any value.


## <a name="mapping"></a>Mapping between OWL and OAS

In this section the correspondences between the [OWL](https://www.w3.org/TR/owl2-quick-reference/) elements and OAS are provided. All maps include examples provided in [Turtle](https://www.w3.org/TR/turtle/) and YAML serializations for readability. The code of both serializations together with an ontology diagram are provided in [examples](examples). In addition, the documentation generated in the Swagger editor is available at [example documentation](ponerenlace).

The prefixes that will be used in this section are:

```prefix
@prefix : <https://w3id.org/example#> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix xml: <http://www.w3.org/XML/1998/namespace> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
```

#### <a name="class"></a>Class

[`OWL`](#owl) | [`OAS`](#oas) | Comments
------ | -------- | --------
`owl:Class` | `Schema Object` | It should be defined as a [Schema Object](#schemaObject) in the [Component Object](#componentsObject) definition. The `Schema Object` must be a`type: object`. The schema name should be the `rdfs:label` value defined in the OWL Class. It is worth noting that such label should not contain blank spaces.
**Class descriptions** |
`owl:intersectionOf` | `allOf` | It should be defined as `allOf` which validates the value against all the subschemas.
`owl:unionOf` | `anyOf` | It should be defined as `anyOf` which validates the value against any (one or more) the subschemas. |
`owl:complementOf` | `not` | It should be defined as `not` which validates the value is not valid against the specified schema |
`owl:oneOf` | `enum` | It should be defined as an `enum` and the values included in this enumeration list. |
**Class axioms** |
`rdfs:subClassOf` | `allOf`| It should be defined as a model composition of the common property set and class-specific. Thus, a subclass should be defined as a [Schema Object](#schemaObject) in the [Component Object](#componentsObject) definition including the field `allOf`. In such field should also be defined a `type: object` and the corresponding [Reference Object](#referenceObject) (`$ref`:'reference to the Parent Class'). Finally, in the `properties` field should be defined the own subclass properties.|
`owl:equivalentClass` | not covered |
`owl:disjointWith` | not covered |

********TODO:*********** especificar qué cosas no estarían cubiertas por ejm la unión de dos intersecciones .


**Class definition example**

This example shows the definition of the Person class. However, its properties have been omitted because the details of the Datatype Properties and Object Properties definition will be provided in the next section.

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

**Subclass definition example**

This example shows the definition of the Professor and Student subclasses. However, the specific properties of each subclass have been omitted because details on how to define Datatype and Object properties will be provided in the following sections.

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

#### <a name="dataTypes"></a>Data Types

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
`owl:DatatypeProperty` | `properties` | It should be defined as a property of the corresponding [Schema Object](#schemaObject) instance. The `Schema Object` must be a `type: object`. The property name will be the `rdfs:label` value defined in the OWL DatatypeProperty. It is worth noting that such label should not contain blank spaces.
`owl:ObjectProperty` | `properties` | It should be defined as a property of the corresponding [Schema Object](#schemaObject) instance. The `Schema Object` must be a `type: object`. The property name will be the `rdfs:label` value defined in the OWL ObjectProperty. It is worth noting that such label should not contain blank spaces.
**Property axioms** |
`rdfs:domain` | `Schema Object`| It should be defined as a [Schema Object](#schemaObject) inside of the [Component Object](#componentsObject) definition. The `Schema Object` must be a `type: object` with a [Reference Object](#referenceObject) (`$ref:`'reference to the Domain Class') . |
`rdfs:range` | `type`| **_a)_** when the range corresponds to an `owl:DatatypeProperty`: if the range is single-valued it should be defined according to its [data type](dataTypes); else the range should be defined compliant to its [data type](dataTypes) and the possible values defined as an enumeration (`enum`). **_b)_** when the range corresponds to an `owl:ObjectProperty`: if the range cardinality >=1 it should be defined as an `array` type and the `items` as a [Reference Object](#referenceObject) (`$ref:`'reference to the Range Class'); else if the range cardinality <1 it could be defined as an `array` type and `items` as a [Reference Object](#referenceObject) (`$ref:`'reference to the single Range Class') or only as an `object` type. This last decision will depend on the developer decision. |
`owl:subPropertyOf` | not covered |
`owl:equivalentProperty` | not covered |
`owl:inverseOf` | not covered |
**Property characteristics** |
`owl:FunctionalProperty` | `maxItems`| ******** ACLARAR ******** It should be defined as the maximum number of items of the array which contains the objects, as follows: `maxItems:` 1. The array will be a `type: object` and contain a [Reference Object](#referenceObject) (`$ref:`'reference to the range Class').   |
`owl:TransitiveProperty` | not covered |
`owl:SymmetricProperty` | not covered |
`owl:AsymmetricProperty` | not covered |

**DatatypeProperty definition example**

The following example presents how to define a Datatype Property of the Person class. A single-value and a multi-value ranges has been included to show their definition in OAS.

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

The next example shows how to define a Datatype Property of the Professor subclass:

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

**ObjectProperty definition example**

In addition, this example shows how to define an Object Property of the Person class:

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


#### <a name="restrictions"></a>Restrictions

[`OWL`](#owl) | [`OAS`](#oas) | Comments
------ | -------- | --------
`owl:onProperty` |  `properties` | It should refers to the property name where the restriction is applied.
`owl:onClass` | `Schema Object` | It should refers to the schema name where the restriction is applied.
`owl:allValuesFrom` | | |
`owl:someValuesFrom` | | |
`owl:hasValue`| | |
`owl:minCardinality` | `minItems` | It should be defined as the minimum number of items from the array which contains the objects.|
`owl:maxCardinality` | `maxItems`| It should be defined as the maximum number of items from the array which contains the objects.|
`owl:cardinality` | | |
`owl:minQualifiedCardinality` | `minItems`| It should be defined as the minimum number of items from the array of `type: object` and a [Reference Object](#referenceObject) (`$ref:`'reference to the restricted Class'). |
`owl:maxQualifiedCardinality` |`maxItems`| It should be defined as the maximum number of items from the array of `type: object` and a [Reference Object](#referenceObject) (`$ref:`'reference to the restricted Class'). |
`owl:qualifiedCardinality`| | |

**Restrictions example**

TTL
```ttl
:Professor rdf:type owl:Class ;
  rdfs:subClassOf :Person ,
    [ rdf:type owl:Restriction ;
      owl:onProperty :teachesTo ;
      owl:allValuesFrom :Student
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
          nullable: true
        worksIn:
          items:
            $ref: '#/components/schemas/University'
          type: array
          maxItems: 1
          nullable: false
```


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

This example shows the `get` operation retrieving all instances of a class.

```YAML
/classlabel:
  get:
    parameters:
      - description: Filter by datatypeLabel
        in: path
        name: datatypeLabel
        required: true
        schema:
          type: string
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
    parameters:
      - description: Filter by resource id
      in: path
      name: id
      required: true
      schema:
        type: string
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
    parameters:
    - description: Filter by name
      in: path
      name: datatypeLabel
      required: false
      schema:
        type: string
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
    parameters:
    - description: Filter by professor id
      in: path
      name: id
      required: true
      schema:
        type: string
    responses:
      '200':
        description: The Professor has been retrieved successfully
        content:
          application/json:
            schema:
              $ref: ‘#/components/schemas/Professor’   
```

#### <a name="metadata"></a>Ontology metadata

 [`OWL`](#owl) | [`OAS`](#oas) | Comments
 ------ | -------- | --------
 `owl:versionInfo` |  `version` | It should described with its value in the `version` field of [Info Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#infoObject).
 `owl:deprecated` |  `deprecated` | It should be applied in the [Schema Object](#schemaObject) `type`    
