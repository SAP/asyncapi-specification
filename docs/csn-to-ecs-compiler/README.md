# CDS CSN to AsyncAPI specification for SAP ecosystem Mapping - Translation Rules

This document shall record and specify the rules for compiling [AsyncAPI specification for SAP ecosystem](../../README.md) documents out of [CDS](https://pages.github.tools.sap/cap/docs/cds/) [CSN](https://pages.github.tools.sap/cap/docs/cds/csn) models.

## Context

The requirement for this has its origin in the Data Plane Services (DPS) and can be found as [CPA JIRA 851](https://jira.tools.sap/browse/CPA-851). Important decisions and concepts are documented in [ADRs](./adrs.md). See also [CLI Options/Flags](./cli-options.md) and [Metadata and Presets](./metadata-and-presets.md). In the meantime, priorities have changed and DPS does not exist as such any longer. However, the requirement is still valid and still applies for instance to Data Warehouse Cloud (DWC).

## Scope

The following mappings are defining CDS CSN to AsyncAPI specification for SAP ecosystem description format translations. AsyncAPI specification for SAP ecosystem format is based on [AsyncAPI 2.0.0](https://www.asyncapi.com/docs/specifications/2.0.0) which uses [JSON Schema Draft 07](https://tools.ietf.org/html/draft-handrews-json-schema-01) to describe data schemas. It is defined as part of [TG27 - Business Events](https://github.tools.sap/CentralEngineering/TechnologyGuidelines/tree/main/tg27) and applies for describing events complying to the [CloudEvents specification for SAP ecosystem](https://github.tools.sap/CentralEngineering/cloudevents-specification), based on [CloudEvents 1.0](https://cloudevents.io/), that are [published to the Event Mesh Kernel Service](https://github.tools.sap/CentralEngineering/TechnologyGuidelines/tree/main/tg27#assessment-tg27r3-publish-business-events-and-event-catalogs). See also [ADR01](./adrs.md#adr01-the-generation-functionality-is-implemented-as-part-of-the-cap-compiler-similar-to-the-existing-openapi-generator) and [ADR02](./adrs.md#adr02-the-compiler-works-specific-to-tg27---business-events-and-its-requirements).

:bulb: Note that the output format is AsyncAPI specification for SAP ecosystem that is based on AsyncAPI 2.0.0. The output format is NOT vanilla AsyncAPI.

## Notations and Terminology

In the given context, CloudEvents are described using AsyncAPI documents. Because CloudEvents and AsyncAPI originated independently from each other, different terminology is used. The following terminology mapping applies:

|CloudEvents term|AsyncAPI term|
|---|---|
|[Event](https://github.tools.sap/CentralEngineering/TechnologyGuidelines/blob/master/tg27/docs/terminology.md#event)|[Message](https://www.asyncapi.com/docs/specifications/2.0.0#definitionsMessage)|
|[Event Context](https://github.tools.sap/CentralEngineering/TechnologyGuidelines/blob/master/tg27/docs/terminology.md#context)|Message Header|
|[Event Data](https://github.tools.sap/CentralEngineering/TechnologyGuidelines/blob/master/tg27/docs/terminology.md#data)|Message Payload|

## Table of Contents

- [Context](#context)
- [Scope](#scope)
- [Notations and Terminology](#notations-and-terminology)
- [Table of Contents](#table-of-contents)
- [Predefined Types](#predefined-types)
  - [Used Formats](#used-formats)
- [Event Definitions](#event-definitions)
  - [Context](#context-1)
  - [Data](#data)
  - [Standard Context](#standard-context)
  - [Example](#example)
- [Type Definitions](#type-definitions)
- [Structured Types](#structured-types)
- [Structured Many Types](#structured-many-types)
- [Arrayed Types](#arrayed-types)
- [Localized Elements](#localized-elements)
- [Temporal Elements (Common Aspect temporal)](#temporal-elements-common-aspect-temporal)
- [Default Values](#default-values)
- [Enums](#enums)
- [Associations](#associations)
  - [Managed to one Associations](#managed-to-one-associations)
  - [Un managed to one Associations](#un-managed-to-one-associations)
  - [One To Many Associations](#one-to-many-associations)
  - [Many To Many Associations](#many-to-many-associations)
- [Compositions](#compositions)
  - [Composition of One](#composition-of-one)
  - [Un Managed Composition of Many](#un-managed-composition-of-many)
  - [Managed Composition of Many](#managed-composition-of-many)
- [Constraints](#constraints)

## Predefined Types

|CDS Type|Arguments/Remarks|JSON-Schema|
|---|---|---|
|UUID|a 36-characters string|`{ "type": "string", "format": "uuid" }`|
|Boolean||`{ "type": "boolean" }`|
|Integer||`{ "type": "integer" }`|
|Integer64||`{ "type": "string", "format": "int64" }`|
|Decimal|( precision, scale )|`{ "type": "string", "format": "decimal", "x-sap-precision": <precision>, "x-sap-scale": <scale> }`|
|Decimal|special case without scale|`{ "type": "string", "format": "decimal", "x-sap-precision": <precision> }`|
|Decimal|special case without precision and scale|`{ "type": "string", "format": "decimal" }`|
|Double||`{ "type": "number" }`|
|Date||`{ "type": "string", "format": "date" }`|
|Time||`{ "type": "string", "format": "partial-time" }`|
|DateTime|sec precision|`{ "type": "string", "format": "date-time" }`|
|Timestamp|µs precision|`{ "type": "string", "format": "date-time" }`|
|String|(length?)|`{ "type": "string", "maxLength": :length }`|
|Binary|(length?)|`{ "type": "string", "maxLength": :length }`|
|LargeBinary||`{ "type": "string" }`|
|LargeString||`{ "type": "string" }`|

### Used Formats

|JSON Schema Type|JSON Schema Format|Specification|Example|
|---|----|---|---|
|string|uuid|UUID in lowercase string representation as defined in [RFC 4122](https://datatracker.ietf.org/doc/html/rfc4122)|`"14c95d8f-9380-445a-99b4-d840268f4849"`|
|string|int64|signed 64-bit integer between `-9,223,372,036,854,775,808` and `9,223,372,036,854,775,807`|`"3155378975999999999"`|
|string|decimal|Arbitrary precise signed decimal number, optionally including `x-sap-precision: int` and/or `x-sap-scale: int` defining precision and scale.|`"3.141592653589793238462643383279"`|
|string|date|Date as defined by [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) `full-date`|`"2021-11-11"`|
|string|partial-time|Partial time as defined by [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) `partial-time` WITHOUT the optional `[time-secfrac]`|`"16:20:00"`|
|string|date-time|Date time as defined by [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) `date-time`|`"2021-11-11T16:20:00Z"`|

## Event Definitions

Each event definition is mapped to a [_message_](https://github.tools.sap/CentralEngineering/asyncapi-specification#messages) object. There will be a dedicated message per event.

Message objects are placed under the `#/components/messages` object. The key and the name property of each message are equal to the event `type` literal (see [Context](#context)). Every message object contains:

- a `headers` object to describe the event context
- a `payload` object to describe the event data
- a `traits` array to describe shared standard context

Additionally, each message is referenced from the `#/channels` section marking the event as active where the direction is outbound (published).
See also [ADR04](./adrs.md#adr04-the-compiler-does-consider-the-events-as-directed-outbound-published).

:bulb: Note that the tooling can only compile the data given to it. It is still the responsibility of the developer to define the events compliant to TG27.

:bulb: Note that as of now, only events defined as part of a `service` are considered. Events outside of a service are ignored and may be considered in a later step.
See also [ADR03](./adrs.md#adr03-the-compiler-does-only-consider-events-declared-as-part-of-a-service).

:bulb: Note that the direction of events is always considered to be outgoing, i.e., they are produced by the service. Incoming events are not considered yet as in CAP they would be defined via an "external service".

### Context

The `headers` object contains the event's [`type`](https://github.tools.sap/CentralEngineering/cloudevents-specification#type-attribute).

The `type` attribute is defined as string literal using the [`const`](https://json-schema.org/understanding-json-schema/reference/generic.html#constant-values) keyword. The string literal of the `type` is concatenated from the namespace, service name and the event name. The developer has to assign a proper name to comply with the CloudEvents specification for SAP ecosystem defined pattern `<namespace>.<businessObject>.<operation>.<version>`. For example, if the event is defined in service `MyService` within namespace `sap.example` with the name `Something.Created.v1`, the resulting `type` is `sap.example.myservice.Something.Created.v1`. See also [ADR07](./adrs.md#adr07-the-compiler-does-construct-the-event-type-from-namespace-service-name-and-event-name).

:bulb: The developer must already choose a compliant event name for TG27 compliance.

### Data

The `payload` object contains the event's data. The schema of the payload is defined under the `#/components/schemas` object and referenced from the `payload` object using `"$ref": "#/components/schemas/:fqn"` (`fqn` fully qualified name of the payload schema). The `fqn` of the payload schema equals the event `type` literal (see [Context](#context)).

### Standard Context

The `traits` array contains the `CloudEventsContext.v1` trait that defines the shared standard context attributes. It is defined under the `#/components/messageTraits` object and referenced in every message using `"$ref": "#/components/messageTraits/CloudEventsContext.v1"`.

<details>

<summary>Click here for the detailed "CloudEventsContext.v1" trait</summary>

```js
{
  "components": {
    "messageTraits": {
      "CloudEventsContext.v1": {
        "headers": {
          "type": "object",
          "properties": {
            "id": {
              "description": "Identifies the event.",
              "type": "string",
              "examples": [
                "6925d08e-bc19-4ad7-902e-bd29721cc69b"
              ]
            },
            "specversion": {
              "description": "The version of the CloudEvents specification which the event uses.",
              "type": "string",
              "const": "1.0"
            },
            "source": {
              "description": "Identifies the instance the event originated in.",
              "type": "string",
              "format": "uri-reference",
              "examples": [
                "/default/sap.s4.beh/ER9CLNT001",
                "/eu/sap.billing.sb/91dec60d-9757-4e2c-b9e5-21da10016fe9"
              ]
            },
            "type": {
              "description": "Describes the type of the event related to the source the event originated in.",
              "type": "string",
              "examples": [
                "sap.dsc.FreightOrder.Arrived.v1",
                "sap.billing.sb.Subscription.Canceled.v1"
              ]
            },
            "subject": {
              "description": "Describes the subject of the event in the context of the source the event originated in (e.g., the id of the business object the event is about).",
              "type": "string",
              "examples": [
                "ce307052-75a0-4a8f-a961-ebf21669bb80",
                "urn:epc:tag:sgtin-96:1.7332402.026591.1234567890"
              ]
            },
            "datacontenttype": {
              "description": "Content type of the event data.",
              "type": "string",
              "const": "application/json"
            },
            "dataschema": {
              "description": "Identifies the schema that the event data adheres to.",
              "type": "string",
              "format": "uri",
              "examples": [
                "http://example.com/event/sap.billing.sb.Subscription.Canceled/v1.2.0"
              ]
            },
            "time": {
              "description": "Timestamp of when the occurrence happened.",
              "format": "date-time",
              "type": "string",
              "examples": [
                "2018-04-05T17:31:00Z"
              ]
            }
          },
          "required": [
            "id",
            "source",
            "specversion",
            "type"
          ],
          "patternProperties": {
            "^xsap[a-z0-9]+$": {
              "description": "Application defined custom extension context attributes.",
              "type": [
                "boolean",
                "integer",
                "string"
              ]
            }
          }
        }
      }
    }
  }
}
```

</details>

### Example

The following example shows how an event defined in CDS CSN is converted to AsyncAPI.
In the remainder of the document, the shown excerpts are simplified to focus on the relevant parts of the definition (i.e., the `message` and `trait` parts or mostly omitted).

In CDS:

```swift
namespace sap.example;

service MyService: {
  event Example.Created.v1: {
    id: Integer;
    example: String(40);
  };
}

```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Example.Created.v1": {
      "kind": "event",
      "elements": {
        "id": {
          "type": "cds.Integer"
        },
        "example": {
          "type": "cds.String",
          "length": 40
        }
      }
    }
  }
}
```

In AsyncAPI:

:bulb: Note:

- message `name` equals the CE `type` and is derived from the `namespace`, `service name` and `event name`
- const for CE `type` context attribute added to headers
  - remaining CE context attributes are merged in via the trait
- payload referencing a schema that is derived from the CDS CSN input

```js
{
  // ...
  "channels": {
    "sap.example.myservice.Example.Created.v1": {
      "subscribe": {
        "message": {
          "$ref": "#/components/messages/sap.example.myservice.Example.Created.v1"
        }
      }
    }
  },
  "components": {
    "messages": {
      "sap.example.myservice.Example.Created.v1": {
        "name": "sap.example.myservice.Example.Created.v1",
        "headers": {
          "properties": {
            "type": {
              "const": "sap.example.myservice.Example.Created.v1"
            }
          }
        },
        "payload": {
          "$ref": "#/components/schemas/sap.example.myservice.Example.Created.v1"
        },
        "traits": [
          {
            "$ref": "#/components/messageTraits/CloudEventsContext.v1"
          }
        ]
      }
    },
    "schemas": {
      "sap.example.myservice.Example.Created.v1": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer"
          },
          "example": {
            "type": "string",
            "maxLength": 40
          }
        }
      }
    },
    "messageTraits": {
      "CloudEventsContext.v1": {
        // ... defined as above, details omitted
      }
    }
  }
}
```

## Type Definitions

Custom defined type definitions will be inlined at the JSON-Schema. Normalization is not used.

In CDS:

```swift
namespace sap.example;

type DisplayId: String(40);

service MyService {
  event Custom.Created.v1: {
    displayId: DisplayId;
  };
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.DisplayId": {
      "kind": "type",
      "type": "cds.String",
      "length": 40
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "elements": {
        "displayId": {
          "type": "sap.example.DisplayId",
          "length": 40
        }
      }
    }
  }
}
```

In the AsyncAPI Document:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "displayId": {
            "type": "string",
            "maxLength": 40
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

## Structured Types

See also [ADR12](./adrs.md#adr13-structured-types-are-objects-and-not-flattened-out).

In CDS:

```swift
namespace sap.example;

type Structure: {
  deeper: {
    a: String(40);
    b: Integer;
  };
};

service MyService {
  event Custom.Created.v1: {
    structure: Structure;
  };
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.Structure": {
      "kind": "type",
      "elements": {
        "deeper": {
          "elements": {
            "a": {
              "type": "cds.String",
              "length": 40
            },
            "b": {
              "type": "cds.Integer"
            }
          }
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "elements": {
        "structure": {
          "type": "sap.example.Structure"
        }
      }
    }
  }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "structure": {
            "type": "object",
            "properties": {
              "deeper": {
                "type": "object",
                "properties": {
                  "a": {
                    "type": "string",
                    "maxLength": 40
                  },
                  "b": {
                    "type": "integer"
                  }
                }
              }
            }
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

## Structured Many Types

In CDS:

```swift
namespace sap.example;

type Structure: {foo: String; bar: String;};

service MyService {
  event Custom.Created.v1: {
    structure: many Structure;
  };
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.Structure": {
      "kind": "type",
      "elements": {
        "foo": {
          "type": "cds.String"
        },
        "bar": {
          "type": "cds.String"
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "elements": {
        "structure": {
          "items": {
            "type": "sap.example.Structure"
          }
        }
      }
    }
  }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "structure": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "foo": { "type": "string" },
                "bar": { "type": "string" }
              }
            }
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

## Arrayed Types

In CDS:

```swift
namespace sap.example;

type Structure: { attribute: String(40); };

type ManyStructure: many Structure;
type ManyAnonymous: many { attribute: String(40); };
type ManyString: many String(40);

service MyService {
  event Custom.Created.v1: {
    manyStructure: ManyStructure;
    manyAnonymous: ManyAnonymous;
    manyString: ManyString;
  };  
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.Structure": {
      "kind": "type",
      "elements": {
        "attribute": {
          "type": "cds.String",
          "length": 40
        }
      }
    },
    "sap.example.ManyStructure": {
      "kind": "type",
      "items": {
        "type": "sap.example.Structure"
      }
    },
    "sap.example.ManyAnonymous": {
      "kind": "type",
      "items": {
        "elements": {
          "attribute": {
            "type": "cds.String",
            "length": 40
          }
        }
      }
    },
    "sap.example.ManyString": {
      "kind": "type",
      "items": {
        "type": "cds.String",
        "length": 40
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "elements": {
        "manyStructure": {
          "type": "sap.example.ManyStructure"
        },
        "manyAnonymous": {
          "type": "sap.example.ManyAnonymous"
        },
        "manyString": {
          "type": "sap.example.ManyString"
        }
      }
    }
  }
}
```

In AsyncAPI Document:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "manyStructure":  {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "attribute": {
                  "type": "string",
                  "maxLength": 40
                }
              }
            }
          },
          "manyAnonymous": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "attribute": {
                  "type": "string",
                  "maxLength": 40
                }
              }
            }
          },
          "manyString": {
            "type": "array",
            "items": {
              "type": "string",
              "maxLength": 40
            }
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

## Localized Elements

In CDS:

```swift
namespace sap.example;

service MyService {
  event Custom.Created.v1: {
    name: localized String(256);
  };
}

```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "elements": {
        "name": {
          "localized": true,
          "type": "cds.String",
          "length": 256
        }
      }
    }
  }
}
```

In AsyncAPI Document:

```js
{
  // ...
  "components": {
    "messages": {
        // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "name": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "lang": {
                  "type": "string",
                  "pattern": "^[a-z]{2}(?:-[A-z]{2})?$"
                },
                "content": {
                  "type": "string",
                  "maxLength": 256
                }
              },
              "required": [
                "lang",
                "content"
              ]
            }
          }
        }
      }
    },
    "messageTraits": {
        // ...
    }
  }
}
```

## Temporal Elements (Common Aspect temporal)

In CDS:

```swift
using { temporal } from '@sap/cds/common';
namespace sap.example;

entity WorkAssignments : temporal {   // temporal details entity
  key ID  : UUID;                 // logical record ID
  role    : String(111);
}

service MyService {
  event Example.Created.v1 : projection on WorkAssignments;
}
```

In CSN:

```swift
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.WorkAssignments": {
      "kind": "entity",
      "includes": [
        "temporal"
      ],
      "elements": {
        "validFrom": {
          "@cds.valid.from": true,
          "type": "cds.Timestamp"
        },
        "validTo": {
          "@cds.valid.to": true,
          "type": "cds.Timestamp"
        },
        "ID": {
          "key": true,
          "type": "cds.UUID"
        },
        "role": {
          "type": "cds.String",
          "length": 111
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Example.Created.v1": {
      "kind": "event",
      "projection": {
        "from": {
          "ref": [
            "sap.example.WorkAssignments"
          ]
        }
      },
      "elements": {
        "validFrom": {
          "@cds.valid.from": true,
          "type": "cds.Timestamp"
        },
        "validTo": {
          "@cds.valid.to": true,
          "type": "cds.Timestamp"
        },
        "ID": {
          "key": true,
          "type": "cds.UUID"
        },
        "role": {
          "type": "cds.String",
          "length": 111
        }
      }
    },
    //...
    "temporal": {
      "kind": "aspect",
      "elements": {
        "validFrom": {
          "@cds.valid.from": true,
          "type": "cds.Timestamp"
        },
        "validTo": {
          "@cds.valid.to": true,
          "type": "cds.Timestamp"
        }
      }
    },
    //...
  }
}
```

In AsyncAPI:

```swift
{
  //...
  "components": {
    "messageTraits": {
      //...
    },
    "messages": {
      //...
    },
    "schemas": {
      "sap.example.myservice.Example.Created.v1": {
        "type": "object",
        "properties": {
          "validFrom": {
            "type": "string",
            "format": "date-time",
            "example": [
              "2017-02-14T20:54:21+00:00"
            ]
          },
          "validTo": {
            "type": "string",
            "format": "date-time",
            "example": [
              "2017-02-14T20:54:21+00:00"
            ]
          },
          "ID": {
            "type": "string",
            "format": "uuid",
            "example": [
              "e78f1eb8-ada8-49b0-8c8f-a5d316e82952"
            ]
          },
          "role": {
            "type": "string",
            "maxLength": 111
          }
        },
        "required": [
          "ID"
        ]
      }
    }
  }
}
```

## Default Values

In CDS:

```swift
namespace sap.example;

service MyService {
  event Custom.Created.v1: {
    defaulted: String(40) default 'value';
  };
}
```

In CSN:

```json
{
    "namespace": "sap.example",
    "definitions": {
        "sap.example.MyService": {
            "@source": "bookshop/srv/test.cds",
            "kind": "service"
        },
        "sap.example.MyService.Custom.Created.v1": {
            "kind": "event",
            "elements": {
                "defaulted": {
                    "type": "cds.String",
                    "length": 40,
                    "default": {
                        "val": "value"
                    }
                }
            }
        }
    }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "defaulted": {
            "type": "string",
            "maxLength": 40,
            "default": "value"
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

## Enums

In CDS:

```swift
namespace sap.example;

type EnumType: String(10) enum { one = 'One'; two = 'Two'; };

service MyService {
  event Custom.Created.v1: {
    name: String(10) enum { one; two };
    typed: EnumType;
  };
}
```

In CSN:

```json
{
    "namespace": "sap.example",
    "definitions": {
        "sap.example.EnumType": {
            "kind": "type",
            "type": "cds.String",
            "length": 10,
            "enum": {
                "one": {
                    "val": "One"
                },
                "two": {
                    "val": "Two"
                }
            }
        },
        "sap.example.MyService": {
            "@source": "bookshop/srv/test.cds",
            "kind": "service"
        },
        "sap.example.MyService.Custom.Created.v1": {
            "kind": "event",
            "elements": {
                "name": {
                    "type": "cds.String",
                    "length": 10,
                    "enum": {
                        "one": {},
                        "two": {}
                    }
                },
                "typed": {
                    "type": "sap.example.EnumType",
                    "length": 10
                }
            }
        }
    }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "name": {
            "type": "string",
            "maxLength": 10,
            "enum": ["one", "two"]
          },
          "typed": {
            "type": "string",
            "maxLength": 10,
            "enum": ["One", "Two"]
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

## Associations

Associated entities are rendered as inline elements.
They will contain only the key attributes. Non key attributes are removed.
They are not normalized. If normalized it could lead to name clashes with full entity descriptions.
See also [ADR09](./adrs.md#adr09-association-are-included-as-objects-containing-the-keys) and [ADR10](./adrs.md#adr10-association-to-many-are-included-as-array-of-objects-containing-the-keys).

### Managed to one Associations

A managed association has a cardinality of one. Technically `one` may be omitted.

```swift
namespace sap.example;

entity Assoc {
  key id: UUID;
  name: String(256);
};

service MyService {
  event Custom.Created.v1: {
    assoc: Association to one Assoc;
  };
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.Assoc": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.UUID"
        },
        "name": {
          "type": "cds.String",
          "length": 256
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "elements": {
        "assoc": {
          "type": "cds.Association",
          "cardinality": {
            "max": 1
          },
          "target": "sap.example.Assoc",
          "keys": [
            {
              "ref": [
                "id"
              ]
            }
          ]
        }
      }
    }
  }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "assoc": {
            "type": "object",
            "properties": {
              "id": {
                "type": "string",
                "format": "uuid",
                "example": [
                  "e78f1eb8-ada8-49b0-8c8f-a5d316e82952"
                ]
              }
            },
            "required": ["id"]
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

### Un managed to one Associations

In CDS:

```swift
namespace sap.example;

entity Assoc {
  key id: UUID;
  name: String(256);
};

service MyService {
  event Custom.Created.v1: {
    assoc: Association to Assoc on assoc.id = assoc_key;
    assoc_key: String;
  };
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.Assoc": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.UUID"
        },
        "name": {
          "type": "cds.String",
          "length": 256
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "elements": {
        "assoc": {
          "type": "cds.Association",
          "target": "sap.example.Assoc",
          "on": [
            {
              "ref": [
                "assoc",
                "id"
              ]
            },
            "=",
            {
              "ref": [
                "assoc_key"
              ]
            }
          ]
        },
        "assoc_key": {
          "type": "cds.String"
        }
      }
    }
  }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "assoc": {
            "type": "object",
            "properties": {
              "id": {
                "type": "string",
                "format": "uuid",
                "example": [
                  "e78f1eb8-ada8-49b0-8c8f-a5d316e82952"
                ]
              }
            },
            "required": ["id"]
          },
          "assoc_key": {
            "type": "string"
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

### One To Many Associations

In CDS

```swift
namespace sap.example;

entity Assoc {
  key id: UUID;
  name: String(256);
};

service MyService {
  event Custom.Created.v1: {
    assoc: Association to many Assoc;
  };
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.Assoc": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.UUID"
        },
        "name": {
          "type": "cds.String",
          "length": 256
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "elements": {
        "assoc": {
          "type": "cds.Association",
          "cardinality": {
            "max": "*"
          },
          "target": "sap.example.Assoc",
          "keys": [
            {
              "ref": [
                "id"
              ]
            }
          ]
        }
      }
    }
  }
}
```

In AsyncAPI

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "assoc": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "id": {
                  "type": "string",
                  "format": "uuid",
                  "example": [
                    "e78f1eb8-ada8-49b0-8c8f-a5d316e82952"
                  ]
                }
              },
              "required": ["id"]
            }
          }
        }
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

### Many To Many Associations

In CDS

```swift
namespace sap.example;

entity Book {
  key bookId : String; 
  authors: Association to many Book2Author;
};

entity Author {
  key authorId: String; 
  name: String; 
  books: Association to many Book2Author
};

entity Book2Author {
  key b2aBook: Association to Book; 
  key b2aAuthor: Association to Author
};

service MyService {
  event Custom.Created.v1: projection on Book;
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.Book": {
      "kind": "entity",
      "elements": {
        "bookId": {
          "key": true,
          "type": "cds.String"
        },
        "authors": {
          "type": "cds.Association",
          "cardinality": {
            "max": "*"
          },
          "target": "sap.example.Book2Author",
          "keys": [
            {
              "ref": [
                "b2aBook"
              ]
            },
            {
              "ref": [
                "b2aAuthor"
              ]
            }
          ]
        }
      }
    },
    "sap.example.Author": {
      "kind": "entity",
      "elements": {
        "authorId": {
          "key": true,
          "type": "cds.String"
        },
        "name": {
          "type": "cds.String"
        },
        "books": {
          "type": "cds.Association",
          "cardinality": {
            "max": "*"
          },
          "target": "sap.example.Book2Author",
          "keys": [
            {
              "ref": [
                "b2aBook"
              ]
            },
            {
              "ref": [
                "b2aAuthor"
              ]
            }
          ]
        }
      }
    },
    "sap.example.Book2Author": {
      "kind": "entity",
      "elements": {
        "b2aBook": {
          "key": true,
          "type": "cds.Association",
          "target": "sap.example.Book",
          "keys": [
            {
              "ref": [
                "bookId"
              ]
            }
          ]
        },
        "b2aAuthor": {
          "key": true,
          "type": "cds.Association",
          "target": "sap.example.Author",
          "keys": [
            {
              "ref": [
                "authorId"
              ]
            }
          ]
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "projection": {
        "from": {
          "ref": [
            "sap.example.Book"
          ]
        }
      },
      "elements": {
        "bookId": {
          "key": true,
          "type": "cds.String"
        },
        "authors": {
          "type": "cds.Association",
          "cardinality": {
            "max": "*"
          },
          "target": "sap.example.Book2Author",
          "keys": [
            {
              "ref": [
                "b2aBook"
              ]
            },
            {
              "ref": [
                "b2aAuthor"
              ]
            }
          ]
        }
      }
    }
  }
}
```

In AsyncAPI

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "bookId": {
            "type": "string"
          },
          "authors": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "b2aBook": {
                  "type": "object",
                  "properties": {
                    "bookId": {
                      "type": "string"
                    }
                  },
                  "required": [
                    "bookId"
                  ]
                },
                "b2aAuthor": {
                  "type": "object",
                  "properties": {
                    "authorId": {
                      "type": "string"
                    }
                  },
                  "required": [
                    "authorId"
                  ]
                }
              },
              "required": [
                "b2aBook",
                "b2aAuthor"
              ]
            }
          }
        },
        "required": [
          "bookId"
        ]
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

## Compositions

Composition of entities and aspects are rendered as inline elements.

### Composition of One

In CDS:

```swift
namespace sap.example;

entity OfOneEntity {
  key id: String;
}

aspect OfOneAspect {
  keyless: String;
}

entity Root {
  key id: String;

  managedOfOneEntity: Composition of one OfOneEntity;
  managedOfOneAspect: Composition of one OfOneAspect;
};

service MyService {
  event Custom.Created.v1: projection on Root;
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.OfOneEntity": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        }
      }
    },
    "sap.example.OfOneAspect": {
      "kind": "aspect",
      "elements": {
        "keyless": {
          "type": "cds.String"
        }
      }
    },
    "sap.example.Root": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        },
        "managedOfOneEntity": {
          "type": "cds.Composition",
          "cardinality": {
            "max": 1
          },
          "target": "sap.example.OfOneEntity",
          "keys": [
            {
              "ref": [
                "id"
              ]
            }
          ]
        },
        "managedOfOneAspect": {
          "type": "cds.Composition",
          "cardinality": {
            "max": 1
          },
          "targetAspect": "sap.example.OfOneAspect",
          "target": "sap.example.Root.managedOfOneAspect",
          "on": [
            {
              "ref": [
                "managedOfOneAspect",
                "up_"
              ]
            },
            "=",
            {
              "ref": [
                "$self"
              ]
            }
          ]
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "projection": {
        "from": {
          "ref": [
            "sap.example.Root"
          ]
        }
      },
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        },
        "managedOfOneEntity": {
          "type": "cds.Composition",
          "cardinality": {
            "max": 1
          },
          "target": "sap.example.OfOneEntity",
          "keys": [
            {
              "ref": [
                "id"
              ]
            }
          ]
        },
        "managedOfOneAspect": {
          "type": "cds.Composition",
          "cardinality": {
            "max": 1
          },
          "targetAspect": "sap.example.OfOneAspect",
          "target": "sap.example.Root.managedOfOneAspect",
          "on": [
            {
              "ref": [
                "managedOfOneAspect",
                "up_"
              ]
            },
            "=",
            {
              "ref": [
                "$self"
              ]
            }
          ]
        }
      }
    },
    "sap.example.Root.managedOfOneAspect": {
      "kind": "entity",
      "elements": {
        "up_": {
          "key": true,
          "type": "cds.Association",
          "cardinality": {
            "min": 1,
            "max": 1
          },
          "target": "sap.example.Root",
          "keys": [
            {
              "ref": [
                "id"
              ]
            }
          ],
          "notNull": true
        },
        "keyless": {
          "type": "cds.String"
        }
      }
    }
  }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string"
          },
          "managedOfOneEntity": {
            "type": "object",
            "properties": {
              "id": {
                "type": "string"
              }
            },
            "required": ["id"]
          },
          "managedOfOneAspect": {
            "type": "object",
            "properties": {
              "keyless": {
                "type": "string"
              }
            }
          }
        },
        "required": [ "id" ]
      }
    },
    "messageTraits": {
      // ...
    }
  }  
}
```

### Un Managed Composition of Many

In CDS:

```swift
namespace sap.example;

entity OfManyEntity {
  key parent: Association to Root;
}

entity Root {
  key id: String;

  unManagedToManyEntity: Composition of many OfManyEntity on unManagedToManyEntity.parent = $self;
};

service MyService {
  event Custom.Created.v1: projection on Root;
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.OfManyEntity": {
      "kind": "entity",
      "elements": {
        "parent": {
          "key": true,
          "type": "cds.Association",
          "target": "sap.example.Root",
          "keys": [
            {
              "ref": [
                "id"
              ]
            }
          ]
        }
      }
    },
    "sap.example.Root": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        },
        "unManagedToManyEntity": {
          "type": "cds.Composition",
          "cardinality": {
            "max": "*"
          },
          "target": "sap.example.OfManyEntity",
          "on": [
            {
              "ref": [
                "unManagedToManyEntity",
                "parent"
              ]
            },
            "=",
            {
              "ref": [
                "$self"
              ]
            }
          ]
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.Custom.Created.v1": {
      "kind": "event",
      "projection": {
        "from": {
          "ref": [
            "sap.example.Root"
          ]
        }
      },
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        },
        "unManagedToManyEntity": {
          "type": "cds.Composition",
          "cardinality": {
            "max": "*"
          },
          "target": "sap.example.OfManyEntity",
          "on": [
            {
              "ref": [
                "unManagedToManyEntity",
                "parent"
              ]
            },
            "=",
            {
              "ref": [
                "$self"
              ]
            }
          ]
        }
      }
    }
  }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string"
          },
          "unManagedToManyEntity": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "parent": {
                  "type": "object",
                  "properties": {
                    "id": {
                      "type": "string"
                    }
                  },
                  "required": [
                    "id"
                  ]
                }
              },
              "required": [
                "parent"
              ]
            }
          }
        },
        "required": [
          "id"
        ]
      }
    },
    "messageTraits": {
      // ...
    }
  }
}
```

### Managed Composition of Many

In CDS:

```swift
namespace sap.example;

aspect OfManyAspect {
  key id: String;
}

entity Root {
  key id: String;

  managedToManyAspect: Composition of many OfManyAspect;
};

service MyService {
  event Custom.Created.v1: projection on Root;
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.OfManyAspect": {
      "kind": "aspect",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        }
      }
    },
    "sap.example.Root": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        },
        "managedToManyAspect": {
          "type": "cds.Composition",
          "cardinality": {
            "max": "*"
          },
          "targetAspect": "sap.example.OfManyAspect",
          "target": "sap.example.Root.managedToManyAspect",
          "on": [
            {
              "ref": [
                "managedToManyAspect",
                "up_"
              ]
            },
            "=",
            {
              "ref": [
                "$self"
              ]
            }
          ]
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.myservice.Custom.Created.v1": {
      "kind": "event",
      "projection": {
        "from": {
          "ref": [
            "sap.example.Root"
          ]
        }
      },
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        },
        "managedToManyAspect": {
          "type": "cds.Composition",
          "cardinality": {
            "max": "*"
          },
          "targetAspect": "sap.example.OfManyAspect",
          "target": "sap.example.Root.managedToManyAspect",
          "on": [
            {
              "ref": [
                "managedToManyAspect",
                "up_"
              ]
            },
            "=",
            {
              "ref": [
                "$self"
              ]
            }
          ]
        }
      }
    },
    "sap.example.Root.managedToManyAspect": {
      "kind": "entity",
      "elements": {
        "up_": {
          "key": true,
          "type": "cds.Association",
          "cardinality": {
            "min": 1,
            "max": 1
          },
          "target": "sap.example.Root",
          "keys": [
            {
              "ref": [
                "id"
              ]
            }
          ],
          "notNull": true
        },
        "id": {
          "key": true,
          "type": "cds.String"
        }
      }
    }
  }
}
```

In AsyncAPI

```js
{
  // ...
  "components": {
    "messages": {
      // ...
    },
    "schemas": {
      "sap.example.myservice.Custom.Created.v1": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string"
          },
          "managedToManyAspect": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "id": {
                  "type": "string"
                }
              },
              "required": ["id"]
            }
          }
        },
        "required": ["id"]
      }
    },
    "messageTraits": {
      // ...
    }
  }
}
```

## Constraints

Keys and attributes having either `@mandatory` annotation or `@Common.FieldControl: #Mandatory` annotation are listed as `required` properties in the JSON Schema.
See also [ADR12](./adrs.md#adr12-keys-and-mandatory-attributes-are-listed-as-required).

Nullable attributes (non-mandatory attributes not having a `not null` constraint) may be omitted completely. They are not explicitly expressed via a union type (`"type": ["<type value>", "null"]`) in the JSON Schema. See [ADR11](./adrs.md#adr11-nullable-attributes-may-be-omitted-and-are-not-explicitly-described-with-null-union-types).

Null handling of attributes with an arrayed type is different from simple types. Sending an explicit null value is not allowed.

In CDS:

```swift
namespace sap.example;

entity WithConstraint {
  key id: String;
  displayId: String(40) @mandatory;
  dummyId: String(10) @Common.FieldControl: #Mandatory;
  nonNullable: String not null;
  nullable: String;
  arrayed: many String;
};

service MyService {
  event WithConstraint.Created.v1: projection on WithConstraint;
}
```

In CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.WithConstraint": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        },
        "displayId": {
          "@mandatory": true,
          "type": "cds.String",
          "length": 40
        },
        "dummyId": {
          "@Common.FieldControl": {
            "#": "Mandatory"
          },
          "type": "cds.String",
          "length": 10
        },
        "nonNullable": {
          "type": "cds.String",
          "notNull": true
        },
        "nullable": {
          "type": "cds.String"
        },
        "arrayed": {
          "items": {
            "type": "cds.String"
          }
        }
      }
    },
    "sap.example.MyService": {
      "@source": "bookshop/srv/test.cds",
      "kind": "service"
    },
    "sap.example.MyService.WithConstraint.Created.v1": {
      "kind": "event",
      "projection": {
        "from": {
          "ref": [
            "sap.example.WithConstraint"
          ]
        }
      },
      "elements": {
        "id": {
          "key": true,
          "type": "cds.String"
        },
        "displayId": {
          "@mandatory": true,
          "type": "cds.String",
          "length": 40
        },
        "dummyId": {
          "@Common.FieldControl": {
            "#": "Mandatory"
          },
          "type": "cds.String",
          "length": 10
        },
        "nonNullable": {
          "type": "cds.String",
          "notNull": true
        },
        "nullable": {
          "type": "cds.String"
        },
        "arrayed": {
          "items": {
            "type": "cds.String"
          }
        }
      }
    }
  }
}
```

In AsyncAPI:

```js
{
  // ...
  "components": {
    "messages": {
      ///
    },
    "schemas": {
      "sap.example.myservice.WithConstraint.Created.v1": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string"
          },
          "displayId": {
            "type": "string",
            "maxLength": 40
          },
          "dummyId": {
            "type": "string",
            "maxLength": 10
          },
          "nullable": {
            "type": "string"
          },
          "arrayed": {
            "type": "array",
            "items": {
              "type": "string"
            }
          }
        },
        "required": [ "id", "displayId", "dummyId" ]
      }
    },
    "messageTraits": {
      "sap.cds.CloudEventsContext.v1": {
        // ...
      }
    }
  }  
}
```
