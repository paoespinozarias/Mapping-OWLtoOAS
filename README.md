# Mapping-OWLtoOAS

Generate an Open API Specification (OAS) from OWL.

This is a document defining how to translate OWL ....

**Authors:** Paola Espinoza-Arias, Daniel Garijo

**Versions:** 1.0.0

**Release date:** 12/05/2020

## Table of Contents

- [Definitions from OAS](#definitions)
  - [Components Object](#componentsObject)
  - [Paths Object](#pathsObject)
  - [Path Item Object](#pathItemObject)
  - [Reference Object](#referenceObject)
  - [Schema Object](#schemaObject)
- [Mapping between OWL and OAS](#mapping)
  - [Class](#class)
  - [Properties](#properties)
  - [Restrictions](#restrictions)
  - [Operations](#operations)
  - [Metadata](#metadata)
  - [Data Types](#dataTypes)





### <a name="definitions"></a>Definitions from OAS

The following definitions have been taken from the [OAS version 3.0.3](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md) as a reduced version of all the details provided in such specification. Only those definitions that will be used in the mapping section are presented.

It is worth nothing that the remaining definitions provided in OAS should be followed in the same manner as are shown in the specification.

#### <a name="componentsObject"></a>Components Object

Holds a set of reusable objects for different aspects of the OAS.
All objects defined within the components object will have no effect on the API unless they are explicitly referenced from properties outside the components object.


###### Fixed Fields

Field Name | Type | Description
---|:---|---
<a name="componentsSchemas"></a> `schemas` | Map[`string`, [Schema Object](#schemaObject) \| [Reference Object](#referenceObject)] | An object to hold reusable [Schema Objects](#schemaObject).

The Components Object supports other fields, however only the detail of `schemas` is listed because this will be used in this mapping. To check the full list of supported fields described in OAS refer to the [Components Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#componentsObject) definition.

#### <a name="pathsObject"></a>Paths Object

Holds the relative paths to the individual endpoints and their operations.
The path is appended to the URL from the [`Server Object`](#serverObject) in order to construct the full URL.  The Paths MAY be empty, due to [ACL constraints](#securityFiltering).

###### Patterned Fields

Field Pattern | Type | Description
---|:---:|---
<a name="pathsPath"></a>/{path} | [Path Item Object](#pathItemObject) | A relative path to an individual endpoint. The field name MUST begin with a forward slash (`/`). The path is **appended** (no relative URL resolution) to the expanded URL from the [`Server Object`](#serverObject)'s `url` field in order to construct the full URL. [Path templating](#pathTemplating) is allowed. When matching URLs, concrete (non-templated) paths would be matched before their templated counterparts. Templated paths with the same hierarchy but different templated names MUST NOT exist as they are identical. In case of ambiguous matching, it's up to the tooling to decide which one to use.

To check more details provided in the OAS refer to [Path Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#paths-object).

#### <a name="pathItemObject"></a>Path Item Object

Describes the operations available on a single path.
A Path Item MAY be empty, due to [ACL constraints](#securityFiltering).
The path itself is still exposed to the documentation viewer but they will not know which operations and parameters are available.

###### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="pathItemRef"></a>$ref | `string` | Allows for an external definition of this path item. The referenced structure MUST be in the format of a [Path Item Object](#pathItemObject).  In case a Path Item Object field appears both in the defined object and the referenced object, the behavior is undefined.
<a name="pathItemSummary"></a>summary| `string` | An optional, string summary, intended to apply to all operations in this path.
<a name="pathItemDescription"></a>description | `string` | An optional, string description, intended to apply to all operations in this path. [CommonMark syntax](https://spec.commonmark.org/) MAY be used for rich text representation.
<a name="pathItemGet"></a>get | [Operation Object](#operationObject) | A definition of a GET operation on this path.
<a name="pathItemPut"></a>put | [Operation Object](#operationObject) | A definition of a PUT operation on this path.
<a name="pathItemPost"></a>post | [Operation Object](#operationObject) | A definition of a POST operation on this path.
<a name="pathItemDelete"></a>delete | [Operation Object](#operationObject) | A definition of a DELETE operation on this path.
<a name="pathItemOptions"></a>options | [Operation Object](#operationObject) | A definition of a OPTIONS operation on this path.
<a name="pathItemHead"></a>head | [Operation Object](#operationObject) | A definition of a HEAD operation on this path.
<a name="pathItemPatch"></a>patch | [Operation Object](#operationObject) | A definition of a PATCH operation on this path.
<a name="pathItemTrace"></a>trace | [Operation Object](#operationObject) | A definition of a TRACE operation on this path.
<a name="pathItemServers"></a>servers | [[Server Object](#serverObject)] | An alternative `server` array to service all operations in this path.
<a name="pathItemParameters"></a>parameters | [[Parameter Object](#parameterObject) \| [Reference Object](#referenceObject)] | A list of parameters that are applicable for all the operations described under this path. These parameters can be overridden at the operation level, but cannot be removed there. The list MUST NOT include duplicated parameters. A unique parameter is defined by a combination of a [name](#parameterName) and [location](#parameterIn). The list can use the [Reference Object](#referenceObject) to link to parameters that are defined at the [OpenAPI Object's components/parameters](#componentsParameters).

To check more details provided in the OAS refer to [Path Item Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#paths-object.
).


#### <a name="referenceObject"></a>Reference Object

A simple object to allow referencing other components in the specification, internally and externally.

The Reference Object is defined by [JSON Reference](https://tools.ietf.org/html/draft-pbryan-zyp-json-ref-03) and follows the same structure, behavior and rules.

For this specification, reference resolution is accomplished as defined by the JSON Reference specification and not by the JSON Schema specification.

###### Fixed Fields
Field Name | Type | Description
---|:---:|---
<a name="referenceRef"></a>`$ref` | `string` | **REQUIRED**. The reference string.

This object cannot be extended with additional properties and any properties added SHALL be ignored.

To check further details described in OAS refer to the [Reference Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#referenceObject) definition.

#### <a name="schemaObject"></a>Schema Object

The Schema Object allows the definition of input and output data types. These types can be objects, but also primitives and arrays.

###### Properties

The following properties are taken directly from the JSON Schema definition and follow the same specifications:

- title
- multipleOf
- maximum
- exclusiveMaximum
- minimum
- exclusiveMinimum
- maxLength
- minLength
- pattern (This string SHOULD be a valid regular expression, according to the [Ecma-262 Edition 5.1 regular expression](https://www.ecma-international.org/ecma-262/5.1/#sec-15.10.1) dialect)
- maxItems
- minItems
- uniqueItems
- maxProperties
- minProperties
- required
- enum

The following properties are taken from the JSON Schema definition but their definitions were adjusted to the OpenAPI Specification.
- type - Value MUST be a string. Multiple types via an array are not supported.
- allOf - Inline or referenced schema MUST be of a [Schema Object](#schemaObject) and not a standard JSON Schema.
- oneOf - Inline or referenced schema MUST be of a [Schema Object](#schemaObject) and not a standard JSON Schema.
- anyOf - Inline or referenced schema MUST be of a [Schema Object](#schemaObject) and not a standard JSON Schema.
- not - Inline or referenced schema MUST be of a [Schema Object](#schemaObject) and not a standard JSON Schema.
- items - Value MUST be an object and not an array. Inline or referenced schema MUST be of a [Schema Object](#schemaObject) and not a standard JSON Schema. `items` MUST be present if the `type` is `array`.
- properties - Property definitions MUST be a [Schema Object](#schemaObject) and not a standard JSON Schema (inline or referenced).
- additionalProperties - Value can be boolean or object. Inline or referenced schema MUST be of a [Schema Object](#schemaObject) and not a standard JSON Schema. Consistent with JSON Schema, `additionalProperties` defaults to `true`.
- description - [CommonMark syntax](https://spec.commonmark.org/) MAY be used for rich text representation.
- format - See [Data Type Formats](#dataTypeFormat) for further details. While relying on JSON Schema's defined formats, the OAS offers a few additional predefined formats.
- default - The default value represents what would be assumed by the consumer of the input as the value of the schema if one is not provided. Unlike JSON Schema, the value MUST conform to the defined type for the Schema Object defined at the same level. For example, if `type` is `string`, then `default` can be `"foo"` but cannot be `1`.

Alternatively, any time a Schema Object can be used, a [Reference Object](#referenceObject) can be used in its place. This allows referencing definitions instead of defining them inline.

Additional properties defined by the JSON Schema specification that are not mentioned here are strictly unsupported.

Other than the JSON Schema subset fields, the following fields MAY be used for further schema documentation:

###### Fixed Fields
Field Name | Type | Description
---|:---:|---
<a name="schemaNullable"></a>nullable | `boolean` | A `true` value adds `"null"` to the allowed type specified by the `type` keyword, only if `type` is explicitly defined within the same Schema Object. Other Schema Object constraints retain their defined behavior, and therefore may disallow the use of `null` as a value. A `false` value leaves the specified or default `type` unmodified. The default value is `false`.
<a name="schemaDiscriminator"></a>discriminator | [Discriminator Object](#discriminatorObject) | Adds support for polymorphism. The discriminator is an object name that is used to differentiate between other schemas which may satisfy the payload description. See [Composition and Inheritance](#schemaComposition) for more details.
<a name="schemaReadOnly"></a>readOnly | `boolean` | Relevant only for Schema `"properties"` definitions. Declares the property as "read only". This means that it MAY be sent as part of a response but SHOULD NOT be sent as part of the request. If the property is marked as `readOnly` being `true` and is in the `required` list, the `required` will take effect on the response only. A property MUST NOT be marked as both `readOnly` and `writeOnly` being `true`. Default value is `false`.
<a name="schemaWriteOnly"></a>writeOnly | `boolean` | Relevant only for Schema `"properties"` definitions. Declares the property as "write only". Therefore, it MAY be sent as part of a request but SHOULD NOT be sent as part of the response. If the property is marked as `writeOnly` being `true` and is in the `required` list, the `required` will take effect on the request only. A property MUST NOT be marked as both `readOnly` and `writeOnly` being `true`. Default value is `false`.
<a name="schemaExample"></a>example | Any | A free-form property to include an example of an instance for this schema. To represent examples that cannot be naturally represented in JSON or YAML, a string value can be used to contain the example with escaping where necessary.
<a name="schemaDeprecated"></a> deprecated | `boolean` | Specifies that a schema is deprecated and SHOULD be transitioned out of usage. Default value is `false`.

This object MAY be extended with [Specification Extensions](#specificationExtensions).

###### <a name="schemaComposition"></a>Composition and Inheritance (Polymorphism)

The OpenAPI Specification allows combining and extending model definitions using the `allOf` property of JSON Schema, in effect offering model composition.
`allOf` takes an array of object definitions that are validated *independently* but together compose a single object.

While composition offers model extensibility, it does not imply a hierarchy between the models.
To support polymorphism, the OpenAPI Specification adds the `discriminator` field.
When used, the `discriminator` will be the name of the property that decides which schema definition validates the structure of the model.
As such, the `discriminator` field MUST be a required field.
There are two ways to define the value of a discriminator for an inheriting instance.
- Use the schema name.
- Override the schema name by overriding the property with a new value. If a new value exists, this takes precedence over the schema name.
As such, inline schema definitions, which do not have a given id, *cannot* be used in polymorphism.

Further details about it are available in the OAS explanation of [Composition and Inheritance](#https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#composition-and-inheritance-polymorphism).

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
`owl:Class` | `Schema Object` | It should be defined as a [Schema Object](#schemaObject) in the [Component Object](#componentsObject) definition. The `Schema Object` must be a`type: object`. The schema name will be the `rdfs:label` value defined in the OWL Class.
**Class descriptions** |
`owl:intersectionOf` | `allOf` | It should be defined as `allOf` which validates the value against all the subschemas.
`owl:unionOf` | `anyOf` | It should be defined as `anyOf` which validates the value against any (one or more) the subschemas. |
`owl:complementOf` | `not` | It should be defined as `not` which validates the value is not valid against the specified schema |
`owl:oneOf` | `enum` | It should be defined as an `enum` and the values included in this enumeration list. |
**Class axioms** |
`rdfs:subClassOf` | `allOf`| It should be defined as a model composition of the common property set and class-specific. The `allOf` must contain the `type: object` with a [Reference Object](#referenceObject) (`$ref`:'reference to the Parent Class') and the own `properties` of the subclass. |
`owl:equivalentClass` | not covered |
`owl:disjointWith` | not covered |

TODO: especificar qué cosas no estarían cubiertas por ejm la unión de dos intersecciones .


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

This example shows the definition of the Professor and Student subclasses. However, the specific properties of each subclass have been omitted because the details of the Datatype Properties and Object Properties definition will be provided in the next section.

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

Primitive data types in the OAS are based on the types supported by the [JSON Schema Specification Wright Draft 00](https://tools.ietf.org/html/draft-wright-json-schema-00#section-4.2).
Note that `integer` as a type is also supported and is defined as a JSON number without a fraction or exponent part.
`null` is not supported as a type (see [`nullable`](#schemaNullable) for an alternative solution).
Models are defined using the [Schema Object](#schemaObject), which is an extended subset of JSON Schema Specification Wright Draft 00.

<a name="dataTypeFormat"></a>Primitives have an optional modifier property: `format`.
OAS uses several known formats to define in fine detail the data type being used.
However, to support documentation needs, the `format` property is an open `string`-valued property, and can have any value.
Formats such as `"email"`, `"uuid"`, and so on, MAY be used even though undefined by this specification.
Types that are not accompanied by a `format` property follow the type definition in the JSON Schema. Tools that do not recognize a specific `format` MAY default back to the `type` alone, as if the `format` is not specified.

In the following, the mapping between data types supported in OWL and those defined by the OAS is presented:

OWL |OAS | Comments
------ | -------- | --------
`xsd:integer`| `integer` |
 `xsd:int`| `integer` | Defining the `formatAuth as `int32` (signed 32 bits)
 `xsd:long`| `integer` | Defining the format as `int64` (signed 64 bits)
 `xsd:float`| `number` | Defining the format as `float` |
 `xsd:double`| `number` |Defining the format as `double` |
 `xsd:string`|`string` |
 `xsd:dateTime`|`string` | Defining the format as `date` (As defined by `full-date` - [RFC3339](https://xml2rfc.ietf.org/public/rfc/html/rfc3339.html#anchor14))
 `xsd:dateTimeStamp`|`string` | Defining the format as `date-time` (As defined by `date-time` - [RFC3339](https://xml2rfc.ietf.org/public/rfc/html/rfc3339.html#anchor14))
 `xsd:byte`|`string` | Defining the format as `byte` (base64 encoded characters)
  `xsd:boolean`|`boolean` |


#### <a name="properties"></a>Properties

[`OWL`](#owl) | [`OAS`](#oas) | Comments
------ | -------- | --------
`owl:DatatypeProperty` | `properties` | It should be defined as a property of the corresponding [Schema Object](#schemaObject) instance. The `Schema Object` must be a `type: object`. The property name will be the `rdfs:label` value defined in the OWL DatatypeProperty.
`owl:ObjectProperty` | `properties` | It should be defined as a property of the corresponding [Schema Object](#schemaObject) instance. The `Schema Object` must be a `type: object`. The property name will be the `rdfs:label` value defined in the OWL ObjectProperty.
**Property axioms** |
`rdfs:domain` | `Schema Object`| It should be defined as a [Schema Object](#schemaObject) inside of the [Component Object](#componentsObject) definition. The `Schema Object` must be a `type: object` with a [Reference Object](#referenceObject) (`$ref:`'reference to the Domain Class') . |
`rdfs:range` | `type`| **_a)_** when the range corresponds to an `owl:DatatypeProperty`: if the range is single-valued it should be defined according to its [data type](dataTypes); else the range should be defined compliant to its [data type](dataTypes) and the possible values defined as an enumeration (`enum`). **_b)_** when the range corresponds to an `owl:ObjectProperty`: if the range cardinality >=1 it should be defined as an `array` type and the `items` as a [Reference Object](#referenceObject) (`$ref:`'reference to the Range Class'); else if the range cardinality <1 it could be defined as an `array` type and `items` as a [Reference Object](#referenceObject) (`$ref:`'reference to the single Range Class') or only as an `object` type. Deciding if  last decision will depend on the developer. |
`owl:subPropertyOf` | not covered |
`owl:equivalentProperty` | not covered |
`owl:inverseOf` | not covered |
**Property characteristics** |
`owl:FunctionalProperty` | `maxItems`| It should be defined as the maximum number of items of the array which contains the objects, as follows: `maxItems:` 1. The array will be a `type: object` and contain a [Reference Object](#referenceObject) (`$ref:`'reference to the range Class').   |
`owl:TransitiveProperty` | not covered |
`owl:SymmetricProperty` | not covered |
`owl:AsymmetricProperty` | not covered |

**DatatypeProperty definition example**

The following example presents how to define a Datatype Property of the Person class:

TTL
```ttl
:identifier rdf:type owl:DatatypeProperty , owl:FunctionalProperty ;
            rdfs:domain :Person ;
            rdfs:range xsd:string ;
            rdfs:label "identifier"@en .
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
```

The next example shows how to define a Datatype Property of the Professor subclass:

TTL
```ttl
:researchField rdf:type owl:DatatypeProperty ;
    rdfs:domain :Person ;
    rdfs:range xsd:string ;
    rdfs:label "researchField"@en .
```
YAML
```yaml
components:
  schemas:
    Person:
      type:object
      properties:
        identifier:
          items:
            type: string
            nullable: false
          type: array
          maxItems: 1
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

In addition, this example shows how to define the Object Property of the Person class:

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
`owl:onClass` | `Schema Object` | It should refers to the Class name where the restriction is applied.
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
`owl:DatatypeProperty`  | `name`  | It should be defined as part of the [Parameter Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#parameter-object) where the list of parameters that are applicable for this operation is provided. Each parameter `name` should correspond to the `rdfs:label` of the Datatype Property. The `schema` of the name corresponds to the `type` used for the operation parameter regarding to the data type of the Datatype Property.
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

#### <a name="metadata"></a>Metadata

 [`OWL`](#owl) | [`OAS`](#oas) | Comments
 ------ | -------- | --------
 `owl:versionInfo` |  `version` | It should described with its value in the `version` field of [Info Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md#infoObject).
 `owl:deprecated` |  `deprecated` | It should be applied in the [Schema Object](#schemaObject) `type`    
