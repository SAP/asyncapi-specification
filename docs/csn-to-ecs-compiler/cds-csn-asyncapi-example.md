CDS:

```swift
namespace sap.example;

entity CatalogService.SampleEntity {
    key id      : UUID;
        boolean : Boolean;
  };

@AsyncAPI.Title        : 'CatalogService Events'
@AsyncAPI.SchemaVersion: '1.0.0'
@AsyncAPI.Description  : 'Events emitted by the CatalogService.'

service CatalogService {
  @AsyncAPI.EventSpecVersion    : '2.0'
  @AsyncAPI.EventCharacteristics: {
    ![state-transfer]: 'full-after-image'
  }
  @AsyncAPI.EventSchemaVersion       : '1.0.0'

  event SampleEntity.Changed.v1 : projection on CatalogService.SampleEntity;
}
```

Presets:

```json5
  // cdsrc
  "cds": {
    "export": {
      "asyncapi": {
        "application_namespace": "sap.example",
        "event_spec_version": "1.0", // will be overwritten with the '@AsyncAPI.EventSpecVersion' annotation in the example
        "event_source": "/{region}/sap.example/{instanceId}"
      }
    }
  }
```

CSN:

```json
{
  "namespace": "sap.example",
  "definitions": {
    "sap.example.CatalogService.SampleEntity": {
      "kind": "entity",
      "elements": {
        "id": {
          "key": true,
          "type": "cds.UUID"
        },
        "boolean": {
          "type": "cds.Boolean"
        }
      }
    },
    "sap.example.CatalogService": {
      "kind": "service",
      "@AsyncAPI.Title": "CatalogService Events",
      "@AsyncAPI.SchemaVersion": "1.0.0",
      "@AsyncAPI.Description": "Events emitted by the CatalogService."
    },
    "sap.example.CatalogService.SampleEntity.Changed.v1": {
      "kind": "event",
      "@AsyncAPI.EventSpecVersion": "2.0",
      "@AsyncAPI.EventCharacteristics.state-transfer": "full-after-image",
      "@AsyncAPI.EventSchemaVersion": "1.0.0",
      "projection": {
        "from": {
          "ref": [
            "sap.example.CatalogService.SampleEntity"
          ]
        }
      },
      "elements": {
        "id": {
          "key": true,
          "type": "cds.UUID"
        },
        "boolean": {
          "type": "cds.Boolean"
        }
      }
    }
  },
  "meta": {
    "creator": "CDS Compiler v3.4.0",
    "flavor": "inferred"
  },
  "$version": "2.0"
}
```

AsyncAPI Document:

```json
{
  "asyncapi": "2.0.0",
  "x-sap-catalog-spec-version": "1.2",
  "x-sap-application-namespace": "sap.example",
  "info": {
    "version": "1.0.0",
    "title": "CatalogService Events",
    "description": "Events emitted by the CatalogService."
  },
  "defaultContentType": "application/json",
  "channels": {
    "sap.example.CatalogService.SampleEntity.Changed.v1": {
      "subscribe": {
        "message": {
          "$ref": "#/components/messages/sap.example.CatalogService.SampleEntity.Changed.v1"
        }
      }
    }
  },
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
    },
    "messages": {
      "sap.example.CatalogService.SampleEntity.Changed.v1": {
        "x-sap-event-spec-version": "2.0",
        "x-sap-event-characteristics": {
          "state-transfer": "full-after-image"
        },
        "x-sap-event-version": "1.0.0",
        "x-sap-event-source": "/{region}/sap.example/{instanceId}",
        "x-sap-event-source-parameters": {
          "region": {
            "description": "The regional context of the application.",
            "schema": {
              "type": "string"
            }
          },
          "applicationNamespace": {
            "description": "The registered namespace of the application.",
            "schema": {
              "type": "string"
            }
          },
          "instanceId": {
            "description": "The instance id (tenant, installation, ...) of the application.",
            "schema": {
              "type": "string"
            }
          }
        },
        "name": "sap.example.CatalogService.SampleEntity.Changed.v1",
        "headers": {
          "type": "object",
          "properties": {
            "type": {
              "const": "sap.example.CatalogService.SampleEntity.Changed.v1"
            }
          }
        },
        "payload": {
          "$ref": "#/components/schemas/sap.example.CatalogService.SampleEntity.Changed.v1"
        },
        "traits": [
          {
            "$ref": "#/components/messageTraits/CloudEventsContext.v1"
          }
        ]
      }
    },
    "schemas": {
      "sap.example.CatalogService.SampleEntity.Changed.v1": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string",
            "format": "uuid",
            "example": [
              "e78f1eb8-ada8-49b0-8c8f-a5d316e82952"
            ]
          },
          "boolean": {
            "type": "boolean"
          }
        },
        "required": [
          "id"
        ]
      }
    }
  }
}
```
