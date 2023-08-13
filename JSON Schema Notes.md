# JSON Schema

> . JSON Schema is an [IETF standard](https://tools.ietf.org/html/draft-zyp-json-schema-04#:~:text=JSON%20Schema%20is%20a%20JSON,interaction%20control%20of%20JSON%20data.) providing a format for what JSON data is required for a given application and how to interact with it. Applying such standards for a JSON document lets you enforce consistency and data validity across similar JSON data.

# Basic Structure

`JSON Schema` is consisted of `Schema Keyword`, `Schema Annotation` and `Validation Keyword`.

```JSON
{
  // Declaring the JSON Schema standard version (dialect)
  "$schema": "https://json-schema.org/draft/2020-12/schema",

  // Declaring the unique identifier as a URI of the schema
  "$id": "https://example.com/product.schema.json",

  // Schema annotation, name of the schema
  "title": "Product",

  // Schema annotation, description of the schema
  "description": "A product in the catalog",

  // Validation keyword, the basic type of data
  "type": "object"
}
```

# Schema annotations

```JSON
{
	"title": "Match anything",
	"description": "This is a schema that matches anything.",
	"default": "Default value",
	"examples": "This is a string sample"
}
```
## `title` and `description`

The `title` and `description` keywords must be strings. 

A "title" will preferably be short, whereas a "description" will provide a more lengthy explanation about the purpose of the data described by the schema.

## `default`

The `default` keyword specifies a default value.

## `example`

The `example` keyword specifies an value or format example.

# Validation keywords

## Type-specific keywords

The `type` keyword is fundamental to JSON Schema. It specifies the data type for a schema.

- string
- number
- integer
- object
- array
- boolean
- null

```JSON
{ "type": "number" }

123         // valid
123.321     // valid
"123"       // invalid
```

```JSON
{ "type": "array" }

[123]       // valid
[null]      // valid
123         // invalid
```

```JSON
{ "type": "object" }

{}                  // valid
{ "a": 123 }        // valid
"abc"               // invalid
["abc"]             // invalid
```

```JSON
{ "type": ["string", "null"] }

"abc"       // valid
null        // valid
["abc"]     // invalid
```


## String-type validation

```JSON
{ "type": "string" }

"abc"           // valid
"排骨 123"      // valid
""              // valid
null            // invalid
123             // invalid
```

### Length validations

```JSON
{
	"type": "string",
	"minLength": 2,
	"maxLength": 3
}

"a"				// invalid
"ab"      		// valid
"abc"           // valid
"abcd"          // invalid
```

### Regular Expressions validations

```JSON
{
	"type": "string",
	"pattern": "^(\\([0-9]{3}\\))?[0-9]{3}-[0-9]{4}$"
}

"555-1212"					// invalid
"(888)555-1212"      		// valid
"(888)555-1212 ext. 532"    // valid
"(800)FLOWERS"         		// invalid
```

### Format validations

- `date-time`: `2018-11-13T20:20:39+00:00`
- `time`: Time, `20:20:39+00:00`
- `date`: Date, `2018-11-13`
- `email`: Internet email address, `user@example.com`, `admin@localhost`
- `ipv4`: IPv4 address, `192.168.0.1`
- `ipv6`: IPv6 address, `::1`, `2001::7334`, `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- `uuid`: UUID/GUID, `3e4666bf-d5e5-4aa7-b8ce-cefe41c7568a`

## Number-type validation

```JSON
{ "type": "integer" }

123				// valid
-100			// valid
123.00			// valid
123.23			// invalid
"123"			// invalid
```

```JSON
{ "type": "number" }

123				// valid
-100			// valid
123.00			// valid
123.23			// valid
"123"			// invalid
```

### Range

- x ≥ `minimum`
- x > `exclusiveMinimum`
- x ≤ `maximum`
- x < `exclusiveMaximum`

```JSON
{ 
	"type": "number",
	"minimum": 0,
	"maximum": 100.11
}

0				// valid
-1				// invalid
100.00			// valid
100.11			// valid
```

```JSON
{ 
	"type": "number",
	"minimum": 0,
	"exclusiveMaximum": 100.11
}

0				// valid
-1				// invalid
100.00			// valid
100.11			// invalid
```

## Boolean-type validations

```JSON
{ "type": "boolean" }

true			// valid
false			// valid
"true"			// invalid
1				// invalid
""				// invalid
```

## Null-type validations

```JSON
{ "type": "null" }

null			// valid
"null"			// invalid
false			// invalid
0				// invalid
""				// invalid
```

## Object-type validations

```JSON
{ "type": "object" }

{ }						// valid
{ "a": null }			// invalid
"object"				// invalid
["object", "array"]		// invalid
```

### Properties

```JSON
{
	"type": "object",
	"properties": {
		"number": { "type": "number" },
		"name": { "type": "string" }
	}
}

{ }													// valid
{ "other": null }									// invalid
{ "number": 123 }									// valid
{ "number": "123" }									// invalid
{ "number": 123, "name": "abc" }					// valid
{ "number": 123, "name": "abc", "other": 123 }		// valid
```

### Additional properties

```JSON
{
	"type": "object",
	"properties": {
		"number": { "type": "number" }
	},
	"additionalProperties": false
}

{ "number": 123 }					// valid
{ "number": 123, "other": null }	// invalid
```

```JSON
{
	"type": "object",
	"properties": {
		"number": { "type": "number" }
	},
	"additionalProperties": { "type": "string" }
}

{ "number": 123, }					// valid
{ "number": 123, "other": "123" }	// valid
{ "number": 123, "other": 123 }		// invalid
```

### Required properties

```JSON
{
	"type": "object",
	"properties": {
		"number": { "type": "number" },
		"name": { "type": "string" }
	},
	"required": ["name"]
}

{ "number": 123 }					// invalid
{ "number": 123, "name": null }		// valid
{ "name": "abc" }					// valid
```

### Property names

```JSON
{
	"type": "object",
	"propertyNames": {
		"pattern": "^[A-Za-z0-9_]*$"
	}
}

{ "number": 123, "123": "number", "number_123": null }		// valid
{ "number 123": 123 }										// invalid
{ "123-number": "abc" }										// invalid
```

## Array-type validations

```JSON
{ "type": "array" }

[1, 2, "3", { "a": 4 }]			// valid
{ "number": 123 }				// invalid
```

### Items

```JSON
{
	"type": "array",
	"items": { "type": "number" }
}

[]					// valid
[1, 2, 3]			// valid
[1, 2, "3"]			// invalid
```

### Contains

```JSON
{
	"type": "array",
	"contains": { "type": "string" }
}

[]					// invalid
[1, 2, 3]			// invalid
[1, 2, "3"]			// valid
```

### Length

```JSON
{
	"type": "array",
	"minItems": 2,
	"maxItems": 2,
}

[]					// invalid
[1, 2]				// invalid
[1, 2, 3]			// invalid
```

### UniqueItems

```JSON
{
	"type": "array",
	"uniqueItems": true
}

[]					// valid
[1, 2]				// valid
[1, 2, 2]			// invalid
```

# Other validation keywords

## `enum` keyword

```JSON
{ "enum": [ 1, "red", true] }

1					// valid
2					// invalid
"red"				// valid
"blue"				// invalid
true				// valid
false				// invalid
```

## `const` keyword

The single value version of `enum`.

```JSON
{ "const": "abc" }

"abc"				// valid
123					// invalid
"cba"				// invalid
```

# Schema composition

## `allOf` keyword

Validate against all of the sub schemas.

```JSON
{
	"allOf": [
		{ "type": "string" },
		{ "maxLength": 3 }
	]
}

"123"			// valid
123				// invalid
"1234"			// invalid
```

## `anyOf` keyword

Validate against some of the sub schemas.

```JSON
{
	"allOf": [
		{ "type": "string", "maxLength": 3 },
		{ "type": "number", "maximum": 123 },
	]
}

"123"			// valid
123				// valid
"1234"			// invalid
1234			// invalid
```

## `oneOf` keyword

Validate against only one of the sub schemas.

```JSON
{
	"oneOf": [
		{ "type": "number", "minimum": 1 },
		{ "type": "number", "maximum": 5 },
	]
}

"123"			// valid
123				// valid
"1234"			// invalid
1234			// invalid
```

## `not` keyword

Do not validate against the sub schema.

```JSON
{
	"not": { "type": "number" }
}

"123"			// valid
true			// valid
null			// valid
1234			// invalid
```

# Schema conditions

## `dependentRequired`

```JSON
{
	"type": "object",
	"properties": [
		"a", { "type": "number" },
		"b", { "type": "number" },
		"c", { "type": "number" }
	],
	"dependentRequired": {
		"c": ["b"]
	}
}

{ "a": 1 }					// valid
{ "b": 2 }					// valid
{ "c": 3 }					// invalid
{ "b": 2, "c": 3 }			// valid
```

## `if`, `then`, `else`

```JSON
{
	"type": "object",
	"properties": [
		"a", { "type": "number" },
		"b", { "type": "number" },
		"c", { "type": "number" }
	],
	"if": { "properties": { "a": { "const": 0 }}},
	"then": { "properties": { "b": { "const": 1 }}},
	"else": { "properties": { "c": { "const": 1 }}}
}

{ "a": 0, "b": 1, "c": 0 }		// valid
{ "a": 0, "b": 0, "c": 0 }		// invalid
{ "a": 1, "b": 1, "c": 1 }		// valid
{ "a": 1, "b": 1, "c": 0 }		// invalid
```

# Complex schemas

## `$ref`

A schema can reference another schema using the `$ref` keyword.

```JSON
{
	"$id": "https://example.com/schemas/customer",
	"type": "object",
	"properties": {
		"first_name": { "type": "string" },
		"last_name": { "type": "string" },
		"shipping_address": { "$ref": "/schemas/address" },
		"billing_address": { "$ref": "/schemas/address" }
	}
}
```

## `$def`


