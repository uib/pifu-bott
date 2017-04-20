# PIFU

PIFU is a Norwegian Standard ([NS
4170:2008](http://www.standard.no/nettbutikk/sokeresultater/?search=4170%3a2008)) for
"Flow of person-related information in education".  The aim is
to make it possible to exchange information about persons, groups,
organizational units, and relationships in the context of education between
different ICT systems, without needing to take into account the products that
are used in the various components.

Ironally the standard is not available on the Web.  Only printouts are allowed
so that [standard.no](http://standard.no) can sell this 70-pages document for
NOK 1000,- a piece.  To make this information somewhat accessible I've
summarized the main points of the standard below:

PIFU is an information model that represents 3 classes of objects:

* _Person_
* _Group_
* _OrgUnit_

and the relationships between them represented by _Relation_ objects.

The following attributes are common for all these objects (including _Relation_):

* TimeFrame?
* Privacy?
* Extension\*

_Group_, _OrgUnit_ and _Relation_ objects also have the attribute:

* Type

_Relation_ objects also have the attribute:

* PrimaryRelation?

_Person_, _Group_ and _OrgUnit_ objects also have these attributes:

* ID+
* Name*
* ContactInfo\*

_Person_ objects also have these attributes:

* Status\*
* DateOfBirth?
* Gender?
* Picture?
* PreferredLanguage?
* NativeTongue\*
* SpecialNeed\*

The cardinality of attributes described above is indicated by the trailing ‘?’,
‘\*’, or ‘+’ character.  The meaning is the same as when they occur in [regular
expressions](https://en.wikipedia.org/wiki/Regular_expression#Basic_concepts).
The ‘?’ means 0 or 1 occurrence.  The ‘\*’ means 0 or more occurrences.  The
‘+’ means 1 or more occurrences.  No cardinality modifier means exactly 1
occurrence (only used for Type above).

From this follows that the only mandatory attribute for _Person_ is ID, while
_Group_ and _OrgUnit_ objects in addition to ID has Type as mandatory
attribute.

The _Relation_ objects can be used in any combination with _Person_, _Group_ and
_OrgUnit_ to create a web of relationships.

The attribute values are themselves objects with structure and their own attributes.

The ID attributes values have the following attributes:

* Type
* Value
* Scope?
* AuthInfo\*
* IsUnique?

where the AuthInfo attribute value have the following attributes:

* Type
* Value

The Name attribute values have the following attributes:

* Type
* Value
* Language?

The ContactInfo attribute values have the following attributes:

* Type
* Value?
* Priority?
* AddressInfo\*
* PostalCode?
* City?

The TimeFrame attribute values have the following attributes:

* From?
* To?
* Extension\*

The Privacy attribute values have the following attributes:

* DoNotDistributeObject?
* DoNotShowObject?
* DoNotDistributeAttribute\*
* DoNotShowAttribute\*
* Extension\*

The Extension attribute values have the following attributes:

* Type
* Value
* Privacy?

The Status attribute values have the following attributes:

* Type
* Value

The Picture attribute values have the following attributes:

* Type
* Format?
* Value

All the rest of the attributes values have the following attributes:

* Content
* Source?

where Content is either a string, a date-time, an integer or a boolean scalar; and Source is a string scalar.

The attributes with date-time Content are From, To, DateOfBirth.

The attributes with integer Content are Priority and PostalCode.

The attributes with boolean Content are DoNotDistributeObject, DoNotShowObject, PrimaryRelation, and IsUnique.

The rest of the basic attribute values have string Content.  These include Type, Value, Format, Language, etc.

PIFU does not specify what encoding to use for values or what Type attribute
values to use.  The standard is supposed to be used with profiles that specify
this and the concrete mapping of the information model to some concrete syntax
based on XML or JSON.

An example PIFU profile is
[PIFU-FK](https://github.com/iktsenteret/pifu-fk-xml) which is an XML based
representation.  There appears to be
[confusion](http://buddysamarbeidet.no/nyheter/1104-buddy-versjon-4) about the
ownership of PIFU-FK, so it might have been superceded by
[PIFU-IMS](https://github.com/iktsenteret/pifu).

## Mapping to JSON

To make this the PIFU model more concrete it might help to try to express it
using the JSON syntax.  No such mapping is specified by the standard itself.
This is just my reinterpretaion of the standard in JSON.

Let’s build the structures bottom up this time and start with the basic
attribute objects representing the primary values which are represented with
objects like:

```json
{
  “Content”: “A string value”,
  “Source”: “ISO 8601”
}
```

in most cases the “Source” attribute isn’t really used and we can simplify this to:

```json
“A string value”
```

For date-time values we use strings like:

```json
“2017-04-20 14:05:30”
```

while integer and boolean values have a natural mapping in JSON.  We don’t use
null values as PIFU has no such concept (just optional attributes).

Attributes that can repeat more than once (suffixed by ‘*’ or ‘+’ in the
attribute listings above) are always represented by JSON arrays.  These arrays
will never be empty, since this will be represented by the attribute itself not
being present.

All other objects and attributes values are represented by JSON objects with
keys that match the attributes in PIFU.

A minimal Person object can thus be represented as:

```json
{
  "ID": [
    {
      "Type": "feideName",
      "Value": "gaa041@uib.no"
    }
  ]
}
```

and if we add some more attributes we end up with:

```json
{
  "ID": [
    {
      "Type": "feideName",
      "Value": "gaa041@uib.no"
    }
  ],
  "Name": [
    {
      "Type": "firstName",
      "Value": "Gisle"
    },
    {
      "Type": "lastName",
      "Value": "Aas"
    }
  ],
  "Gender": {
    "Content": 1,
    "Source": "ISO/IEC 5218"
  },
  "PreferredLanguage": {
    "Content": "nb",
    "Source": "ISO 639-1"
  }
}
```

The Type values used above isn't part of the PIFU standard, but is used in some of the examples
in the standard.  For a more comprehensive example take a look at:

* [janne-pifu-person.json](janne-pifu-person.json)
