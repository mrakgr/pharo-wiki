# JSON support in Pharo
Currently, Pharo provides two main frameworks to handle the [JSON format](https://en.wikipedia.org/wiki/JSON).

This page briefly present these two frameworks and expose their differences to help users to choose the one fitting their needs.

- [STONJSON](#stonjson)
  * [Parse JSON](#parse-json)
  * [Generate JSON](#generate-json)
- [NeoJSON](#neojson)
  * [Install](#install)
  * [Parse JSON](#parse-json-1)
  * [Generate JSON](#generate-json-1)
- [STONJSON v.s. NeoJSON](#stonjson-vs-neojson)
- [JSON Schema](#json-schema)

## STONJSON
STONJSON is the built-in JSON parser available in default Pharo images. It is part of the STON package and its development takes place in Pharo's [github repository](https://github.com/pharo-project/pharo).

### Parse JSON
- From `String`:
```Smalltalk
STONJSON fromString: '{ "foo" : 42.0 }' "a Dictionary('foo'->42.0 )"
```

- From `Stream`:
```Smalltalk
readStream := '{ "foo" : 42.0 }' readStream.
STONJSON fromStream: readStream "a Dictionary('foo'->42.0 )"
```

- Read from JSON file:
```Smalltalk
'/path/to/file.json' asFileReference
	readStreamDo: [ :readStream |
		STONJSON fromStream: readStream ] "a Dictionary('foo'->42.0 )"
```

### Generate JSON
- Let `jsonObject` be defined as:
```Smalltalk
jsonObject := Dictionary new
		at: 'foo' put: 42.0;
		yourself.
```

- To generate a JSON `String`:
```Smalltalk
STONJSON toString: jsonObject "'{""foo"":42.0}'"
```

- To generate JSON on a `Stream`:
```Smalltalk
STONJSON put: jsonObject onStream: writeStream
```

- To write a JSON file:
```Smalltalk
'/path/to/file.json' asFileReference
	writeStreamDo: [ :writeStream |
		STONJSON put: jsonObject onStream: writeStream ]
```

To pretty print JSON, either use `STONJSON>>#toStringPretty:` or `STONJSON>>#put:onStreamPretty:`.

## NeoJSON
NeoJSON is actually maintained by Sven Van Caekenberghe on [github](https://github.com/svenvc/NeoJSON). 
This section shows some quick examples but there is a great [documentation made by Sven](https://github.com/svenvc/docs/blob/master/neo/neo-json-paper.md) and a chapter in [Enterprise Pharo (chapter 8)](http://books.pharo.org/enterprise-pharo/).

### Install
To install NeoJSON, simply execute the following code snippet in a playground:
```
Metacello new
    repository: 'github://svenvc/NeoJSON/repository';
    baseline: 'NeoJSON';
    load
```

### Parse JSON

- From `String`:
```Smalltalk
NeoJSONReader fromString: '{ "foo" : 42.0 }' "a Dictionary('foo'->42.0 )"
```

- From `Stream`:
```Smalltalk
readStream := '{ "foo" : 42.0 }' readStream.
(NeoJSONReader on: readStream) next
```

- Read from JSON file:
```Smalltalk
'/path/to/file.json' asFileReference
	readStreamDo: [ :readStream |
		(NeoJSONReader on: readStream) next ] "a Dictionary('foo'->42.0 )"
```

### Generate JSON
Let `jsonObject` be defined as:
```Smalltalk
jsonObject := Dictionary new
		at: 'foo' put: 42.0;
		yourself.
```

- To generate a JSON `String`:
```Smalltalk
NeoJSONWriter toString: jsonObject "'{""foo"":42.0}'"
```

Pretty:
```Smalltalk
NeoJSONWriter toStringPretty: jsonObject
```

- To generate JSON on a `Stream`:
```Smalltalk
(NeoJSONWriter on: writeStream)
	nextPut: jsonObject
```

Pretty:
```Smalltalk
(NeoJSONWriter on: writeStream)
	prettyPrint: true;
	nextPut: jsonObject.
```

- To write a JSON file:
```Smalltalk
'/path/to/file.json' asFileReference
	writeStreamDo: [ :writeStream |
		(NeoJSONWriter on: writeStream)
			nextPut: jsonObject ]
```

Pretty:
```Smalltalk
'/path/to/file.json' asFileReference
	writeStreamDo: [ :writeStream |
		(NeoJSONWriter on: writeStream)
			prettyPrint: true;
			nextPut: jsonObject ]
```

## STONJSON v.s. NeoJSON

|Property                                        |STONJSON            |NeoJSON               |
|------------------------------------------------|--------------------|----------------------|
|Parse JSON                                      | :white_check_mark: | :white_check_mark:   |
|Generate JSON                                   | :white_check_mark: | :white_check_mark:   |
|Pretty-printing                                 | :white_check_mark: | :white_check_mark:   |
|Built-in                                        | :white_check_mark: | :x:                  |
|Facilities to map JSON objects to Pharo objects | :x:                | :white_check_mark:   |
|Facilities to query JSON objects                | :x:                | :white_check_mark: * |

> *See [this post](http://forum.world.st/How-to-query-a-JSON-tp4948175p4948177.html) from Sven on the mailing list to know how to use this feature implemented in `NeoJSONObject`.

## JSON Schema
JSON Schema allows to describes the structure of JSON objects. It is similar to [XML Schema](https://en.wikipedia.org/wiki/XML_Schema_(W3C)) but for JSON.

A yet incomplete (but working in its scope) implementation of JSON Schema is provided by Zweidenker [on github](https://github.com/zweidenker/JSONSchema).
