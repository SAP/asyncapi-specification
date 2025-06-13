# CDS CSN to AsyncAPI specification for SAP ecosystem Mapping - Presets and Annotations

Concept document implementing the "preset" approach for adding necessary metadata to the CSN->Event Catalog Mapping. See [discussion document](https://sap.sharepoint.com/:w:/t/CPAIntegration/EV-nxtusoERIi2FmMGYyyQcBFx1EC6rBWjS-TGRjw-MSqg?e=u9Y8OR) for the full context.

## Convention

Three approaches are considered for providing the info necessary:

- preset
- annotation
- tooling const

Each section starts with a "rationale" explaining why the approach was chosen.

## Validation

Many of the attributes below are mandatory for a valid AsyncAPI specification for SAP ecosystem document (some conditionally, e.g., based on the value of another attribute). In case there is no reasonable default for an attribute, it is omitted instead of providing a useless default. Reason being that an eventual validation should rather fail than succeed based on a meaningless default value that potentially causes problems after a document has been published, e.g., on SAP Business Accelerator Hub.

AsyncAPI specification for SAP ecosystem support is currently in the process of being built into the [SAP API Metadata Validator](https://github.tools.sap/CentralEngineering/api-metadata-validator). Tool will be used for instance by SAP Business Accelerator Hub to validate compliance when publishing.

## Document Level

### [`info`](https://github.tools.sap/CentralEngineering/asyncapi-specification#info) object

#### `info.title`

Rationale: this is specific to each `service` / event catalog. A pre set applying to all `services` and therefore event catalogs doesn't make sense.

- approach: annotation
- default: none, compiler fails if it cannot be determined :exclamation:
- define with: custom annotation `@AsyncAPI.Title: <value>`
  - common annotation [`@title`](https://pages.github.tools.sap/cap/docs/cds/annotations#general-purpose) cannot be used as it may lead to conflicts
- target: `service`
- merged option: must be specified as preset `#cds/export/asyncapi/merged/title`, compiler fails if not present :exclamation:

Example:

```swift
@AsyncAPI.Title: 'CatalogService Events'
service CatalogService
```

#### `info.version`

Rationale: this is specific to each `service` / event catalog. A pre set applying to all `services` and therefore event catalogs doesn't make sense.

- approach: annotation
- default: none, compiler fails if it cannot be determined :exclamation:
- define with: custom annotation `@AsyncAPI.SchemaVersion: <value>`
  - `@SchemaVersion` is also used for [OpenAPI conversion](https://pages.github.tools.sap/cap/docs/advanced/openapi#core-annotations), but we cannot re-use it as a service may expose both APIs and events. Using the same annotation for defining versions for both would couple the versions together which is a bad idea.
- target: `service`
- merged option: must be specified as preset `#cds/export/asyncapi/merged/version`, compiler fails if not present :exclamation:

Example:

```swift
@AsyncAPI.SchemaVersion: '1.0.0'
service CatalogService
```

#### `info.description`

Rationale: this is specific to each `service` / event catalog. A pre set applying to all `services` and therefore event catalogs doesn't make sense.

- approach: annotation
- default: omitted
- define with: custom annotation [`@AsyncAPI.Description`](https://pages.github.tools.sap/cap/docs/cds/annotations#general-purpose) annotation
- merged option: may be specified as preset `#cds/export/asyncapi/merged/description`, defaults if not provided

Example:

```swift
@AsyncAPI.Description: 'Events emitted by the CatalogService.'
service CatalogService
```

### [`x-sap-catalog-spec-version`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-catalog-spec-version)

Rationale: this must be determined by the tooling, depending on what version it supports (may evolve over time).

- approach: tooling const
- const: `1.2`

### [`x-sap-application-namespace`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-application-namespace)

**TODO :construction: This will be deferred until the concrete integration with EMKS service plans is tackled. In the meantime, a CLI param can be used.**

CAP messaging [derives](https://pages.github.tools.sap/cap/docs/guides/messaging/event-mesh?q=cloudevents#special-topic-rewriting-applied) the `source` from the "publishPrefix".
At runtime this publishPrefix is in turn derived from the `namespace` of the enterprise messaging binding.

We could leverage the same to parse the application namespace. However, we are not at runtime and would need to rely on an explicitly set publishPrefix in `cds.requires.messaging.publishPrefix` (how to get the one of kind enterprise-messaging and format cloudevents?). Not sure if this is reasonable though?

Looking ahead and considering the new internal service plans of Event Mesh Kernel Service, the binding will also include a `ceSource` attribute.

### [`x-sap-stateInfo`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-stateinfo)

Rationale: this is specific to each `service`. A pre set applying to all `services` doesn't make sense.

- approach: annotation
- default: omitted
- define with:
  ```swift
  @AsyncAPI.StateInfo: {
    state: '<value>',
    deprecationDate: '<value>',
    decomissionedDate: '<value>',
    link: '<value>'
  }
  ```
- target: `service`

Example:

```swift
@AsyncAPI.StateInfo: {
  state: 'DECOMMISSIONED',
  deprecationDate: '2022-08-31',
  decomissionedDate: '2024-08-31',
  link: 'http://some.release.notes.sap.com'
}
service CatalogService @(path:'/browse') {
```

### [`x-sap-shortText`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-shorttext-1)

Rationale: this is specific to each `service` / event catalog. The annotation is used mainly by SAP Business Accelerator Hub for displaying in the UI. It is optional.

- approach: annotation
- default: none
- define with: custom annotation `@AsyncAPI.ShortText: <value>`
- target: `service`
- merged option: can be specified as preset `#cds/export/asyncapi/merged/short_text`

Example:

```swift
@AsyncAPI.ShortText: 'CatalogService Events'
service CatalogService
```

## Event Level

### [`x-sap-event-spec-version`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-event-spec-version)

Rationale: it is likely that all events follow the same specversion, however, the tooling cannot know what version is state of the art at the time using it. Therefore, the default is to omit it.

- approach: preset
- default: omitted
- define with:
  ```json5
  // cdsrc
  "cds": {
    "export": {
      "asyncapi": {
        "event_spec_version": "<value>"
      }
    }
  }
  ```
- overwrite with:
  - annotation `@AsyncAPI.EventSpecVersion: '<value>'`

:exclamation: a missing `x-sap-event-spec-version` may fail eventual catalog validation.

Example:

```json5
// cdsrc
"cds": {
  "export": {
    "asyncapi": {
      "event_spec_version": "2.0"
    }
  }
}
```

```swift
@AsyncAPI.EventSpecVersion: '2.0'
event Book.Updated.v1 : projection on Book;
```

:bulb: version 2.0 has been released in September 2022.

### [`x-sap-event-source`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-event-source)

Rationale: we can provide the very generic preset which may be overwritten with a more precise template. Individual values for different events may happen (e.g., sub context namespaces).

:bulb: note that this is related to [`x-sap-application-namespace`](#x-sap-application-namespace). If we have a reasonable value there, we could derive it here, but a generic pattern for the start should suffice.

:bulb: note that this is related to [`x-sap-event-source-parameters`](#x-sap-event-source-parameters).

- approach: preset
- default: `/{region}/{applicationNamespace}/{instanceId}`
- define with:
  ```json5
  // cdsrc
  "cds": {
    "export": {
      "asyncapi": {
        "event_source": "<value>"
      }
    }
  }
  ```
- overwrite with:
  - annotation `@AsyncAPI.EventSource: '<value>'`

Example:

```json5
// cdsrc
"cds": {
  "export": {
      "asyncapi": {
        "event_source": "/{region}/sap.s4/{instanceId}"
      }
    }
}
```

```swift
@AsyncAPI.EventSource: '/{region}/sap.app/{instanceId}'
event Book.Updated.v1 : projection on Book;
```

### [`x-sap-event-source-parameters`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-event-source-parameters)

Rationale: we can provide the very generic preset which may be overwritten with a more precise template. Individual values for different events may happen (sub context namespaces).

:bulb: note that this is related to [`x-sap-event-source`](#x-sap-event-source).

- approach: preset
- default:
  ```json5
  {
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
  }
  ```
- define with:
  ```json5
  // cdsrc
  "cds": {
    "export": {
      "asyncapi": {
        "event_source_params": {
          "region": "<region definition ...>",
          "instanceId": "<instanceId definition ...>"
        }
      }
    }
  }
  ```
- overwrite with:
  - annotation
    ```swift
    @AsyncAPI.EventSourceParams: {
      region: '<region definition ...>',
      instanceId: '<instanceId definition ...>'
    }
    ```

Example:

```json5
// cdsrc
"cds": {
  "export": {
    "asyncapi": {
      "event_source_params": {
        "region": {
          "description": "The regional context of the application.",
          "schema": {
            "type": "string",
            "enum": ["eu", "us"]
          }
        },
        "instanceId": {
          "description": "The instance id (tenant, installation, ...) of the application.",
          "schema": {
            "type": "string"
          }
        }
      }
    }
  }
}
```

```swift
@AsyncAPI.EventSourceParams: {
  region: {
    description: 'The regional context of the application.',
    schema: {
      type: 'string',
      enum: ['eu', 'us']
    }
  },
  instanceId: {
    description: 'The instance id (tenant, installation, ...) of the application.',
    schema: {
      type: 'string'
    }
  }
}
event Book.Updated.v1 : projection on Book;
```

### [`x-sap-event-characteristics`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-event-characteristics)

Rationale: it is likely that many events provide the same characteristics, but exceptions may exist. We can however not assume any default.

- approach: preset
- default: omitted
- define with:
  ```json5
  // cdsrc
  "cds": {
    "export": {
      "asyncapi": {
        "event_characteristics": {
          "<key>": "<value>",
          "<key2>": "value2>",
          // ...
        }
      }
    }
  }
  ```
- overwrite with:
  ```swift
  @AsyncAPI.EventCharacteristics: {
    key: '<value>'
    // ...
  }
  ```
  - :bulb: will fully disable what was specified as preset
  - `key` must be escaped using syntax `![key]` in case hyphens are used

Examples:

```json5
// cdsrc
"cds": {
  "asyncapi": {
    "event_characteristics": {
      "instance-identification": "key-subject", 
      "state-transfer": "full-after-image" 
    }
  }
}
```

```swift
@AsyncAPI.EventCharacteristics: {
  ![instance-identification]: 'key-subject'
}
event BookDeleted : projection on Book { id };
```

### [`x-sap-stateInfo`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-stateinfo-1)

Annotation: @AsyncAPI.EventStateInfo
Rationale: this is specific to each `event`. A pre set applying to all `events` doesn't make sense.

This is the same as [`x-sap-stateInfo`](#x-sap-stateinfo) but the target of the annotation is an `event`.

### [`x-sap-event-version`](https://github.tools.sap/CentralEngineering/asyncapi-specification#x-sap-event-version)

Annotation: @AsyncAPI.EventSchemaVersion
Rationale: this is specific to each `event`. A possible scenario is a first release where all events are in `1.0.0`. But not allowing that as preset as this would be error prone.

This is the same as [`info.version`](#infoversion) but the target of the annotation is an `event`.

:exclamation: a missing `x-sap-event-version` may fail eventual catalog validation.

### developer-defined extensions

See [ADR15](./adrs.md#adr15-asyncapiextensions-can-be-used-for-developer-defined-extensions) for details.

- approach: annotation
- default: omitted
- define with:
  ```swift
  @AsyncAPI.Extensions: {
    ![foo-bar]: 'baz'
  }
  ```
- target: `service`, `event`

Example:

```swift
@AsyncAPI.Extensions: {
  ![foo-bar]: 'baz'
}
service CatalogService {
  // ...
}
```

Generates

```json5
{
  "asyncapi": "2.0.0",
  "x-foo-bar": "baz"
  // ...
}
```
