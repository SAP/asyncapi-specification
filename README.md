[![REUSE status](https://api.reuse.software/badge/github.com/SAP/asyncapi-specification)](https://api.reuse.software/info/github.com/SAP/asyncapi-specification)

# AsyncAPI specification for SAP ecosystem - 1.2

The _AsyncAPI specification for SAP ecosystem_ describes events that comply with the _CloudEvents specification for SAP ecosystem_.

The goal of this specification is to describe a standardized event catalog format that enables a central and uniform event publication and event discovery.

Is's used to publish content on [SAP Business Accelerator Hub](https://api.sap.com).

This specification is based on the [AsyncAPI Specification 2.0](https://v2.asyncapi.com/docs/reference/specification/v2.0.0). Every document following the AsyncAPI specification for SAP ecosystem is also a valid AsyncAPI 2 document.

It is RECOMMENDED to first get familiar with AsyncAPI 2.0.

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Contact Persons](#contact-persons)
- [Notational Conventions](#notational-conventions)
- [Terminology](#terminology)
- [Specification](#specification)
  - [AsyncAPI Object](#asyncapi-object)
    - [`asyncapi`](#asyncapi)
    - [`id`](#id)
    - [`info`](#info)
      - [`version`](#version)
      - [`title`](#title)
      - [`description`](#description)
    - [`servers`](#servers)
    - [`channels`](#channels)
    - [`components`](#components)
    - [`x-sap-catalog-spec-version`](#x-sap-catalog-spec-version)
    - [`x-sap-application-namespace`](#x-sap-application-namespace)
    - [`x-sap-ord-id`](#x-sap-ord-id)
    - [`x-sap-stateInfo`](#x-sap-stateinfo)
    - [`x-sap-shortText`](#x-sap-shorttext)
  - [Channels Object](#channels-object)
  - [Channel Item Object](#channel-item-object)
    - [`subscribe`](#subscribe)
    - [`publish`](#publish)
  - [Operation Object](#operation-object)
    - [`message`](#message)
  - [Components Object](#components-object)
    - [`messages`](#messages)
    - [`schemas`](#schemas)
  - [Message Object](#message-object)
    - [`name`](#name)
    - [`headers`](#headers)
    - [`payload`](#payload)
    - [`traits`](#traits)
    - [`x-sap-event-spec-version`](#x-sap-event-spec-version)
    - [`x-sap-event-source`](#x-sap-event-source)
    - [`x-sap-event-source-parameters`](#x-sap-event-source-parameters)
    - [`x-sap-event-characteristics`](#x-sap-event-characteristics)
    - [`x-sap-stateInfo`](#x-sap-stateinfo-1)
    - [`x-sap-event-version`](#x-sap-event-version)
  - [Message Trait Object](#message-trait-object)
    - [`CloudEventsContext.v1` messageTrait Template](#cloudeventscontextv1-messagetrait-template)
- [Defined Specification Extensions](#defined-specification-extensions)
  - [`x-sap-catalog-spec-version`](#x-sap-catalog-spec-version-1)
  - [`x-sap-event-spec-version`](#x-sap-event-spec-version-1)
  - [`x-sap-event-source`](#x-sap-event-source-1)
  - [`x-sap-event-source-parameters`](#x-sap-event-source-parameters-1)
  - [`x-sap-object-type`](#x-sap-object-type)
  - [`x-sap-odm-version`](#x-sap-odm-version)
  - [`x-sap-logical-odm-event-version`](#x-sap-logical-odm-event-version)
  - [`x-sap-event-characteristics`](#x-sap-event-characteristics-1)
  - [`x-sap-application-namespace`](#x-sap-application-namespace-1)
  - [`x-sap-stateInfo`](#x-sap-stateinfo-2)
  - [`x-sap-event-version`](#x-sap-event-version-1)
  - [`x-sap-shortText`](#x-sap-shorttext-1)
  - [`x-sap-software-min-version`](#x-sap-software-min-version)
- [Defined JSON Schema Extensions](#defined-json-schema-extensions)
  - [`x-key`](#x-key)
  - [`x-sap-odm-entity-name`](#x-sap-odm-entity-name)
  - [`x-sap-odm-oid-reference-entity-name`](#x-sap-odm-oid-reference-entity-name)
  - [`x-sap-dpp-entity-semantics`](#x-sap-x-sap-dpp-entity-semantics)
  - [`x-sap-dpp-data-subject-role`](#x-sap-dpp-data-subject-role)
  - [`x-sap-dpp-data-subject-role-description`](#x-sap-dpp-data-subject-role-description)
  - [`x-sap-dpp-field-semantics`](#x-sap-dpp-field-semantics)
  - [`x-sap-dpp-is-potentially-personal`](#x-sap-dpp-potentially-personal)
  - [`x-sap-dpp-is-potentially-sensitive`](#x-sap-dpp-is-potentially-sensitive)  
- [Event Catalog Compatibility, Versioning and Lifecycle](#event-catalog-compatibility-versioning-and-lifecycle)
  - [Compatibility and Versioning](#compatibility-and-versioning)
    - [Patch Changes](#patch-changes)
    - [Minor Changes](#minor-changes)
    - [Major Changes](#major-changes)
  - [Lifecycle and Deprecation](#lifecycle-and-deprecation)
- [Examples](#examples)
- [Tools and Resources](#tools-and-resources)
- [Changelog](#changelog)

## Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

## Terminology

The CloudEvents specification for SAP ecosystem and the AsyncAPI specification for SAP ecosystem are based on the open standards CloudEvents 1.0 and AsyncAPI 2.0. Both standards use a different terminology and the following mapping applies:

|CloudEvents specification for SAP ecosystem|AsyncAPI specification for SAP ecosystem|
|---|---|
|Event|[Message](https://www.asyncapi.com/docs/specifications/2.0.0#definitionsMessage)|
|Event Context|[Message Header](https://www.asyncapi.com/docs/specifications/2.0.0#messageObject)|
|Event Data|[Message Payload](https://www.asyncapi.com/docs/specifications/2.0.0#messageObject)|

Documents following the AsyncAPI specification for SAP ecosystem are also referred to as _event catalogs_ or _event catalog documents_ in the following.

The primary aim of this specification is to allow producing applications to document the events they are producing. It is also possible to describe the events that are consumed by the application.

## Specification

An event catalog document MUST always be a valid AsyncAPI 2.0 document. All normative statements from the AsyncAPI 2.0 specification apply. This specification defines additional and more specific normative statements on individual object and field level which are defined below.

An event catalog document according to this specification MUST only be used to describe events that follow the CloudEvents specification for SAP ecosystem.

Event catalog documents MUST NOT be used to describe any other asynchronous API. Instead, regular AsyncAPI 2.0 documents SHOULD be used to describe those (in this case, the [`servers`](#servers) field SHOULD be completed to indicate the intermediary providing the described events).

### [AsyncAPI Object](https://www.asyncapi.com/docs/specifications/2.0.0#A2SObject)

#### `asyncapi`

The `asyncapi` field MUST be set to the value `2.0.0`.

#### `id`

The `id` field SHOULD NOT be set in this version of the specification as it might be
used in a later version. A consumer of the event catalog document therefore MUST NOT expect the `id` field to be defined or to transport any semantics if it is defined.

#### [`info`](https://www.asyncapi.com/docs/reference/specification/v2.0.0#infoObject)

##### `version`

The `version` MUST reflect the version of the catalog document according to the rules described in [Event Catalog Compatibility, Versioning and Lifecycle](#event-catalog-compatibility-versioning-and-lifecycle).

##### `title`

The `title` is displayed by the SAP Business Accelerator Hub and MUST adhere to the constraints defined for `title` in the ORD Document's [`Event Resource`](https://open-resource-discovery.github.io/specification/spec-v1/interfaces/document#event-resource), including the  [SAP Core Policy Level (v1.0)](https://open-resource-discovery.github.io/specification/spec-extensions/policy-levels/sap-core-v1#title-constraints).

##### `description`

The `description` is displayed by the SAP Business Accelerator Hub and MUST adhere to the constraints defined for `description` in the ORD Document's [`Event Resource`](https://open-resource-discovery.github.io/specification/spec-v1/interfaces/document#event-resource), including the  [SAP Core Policy Level (v1.0)](https://open-resource-discovery.github.io/specification/spec-extensions/policy-levels/sap-core-v1#description-constraints).

#### `servers`

As the event catalog document is currently provided by the event producer,
the `servers` field SHOULD NOT be added to the document.

It is implied that consumers of an event catalog document MUST connect to the [SAP Cloud Application Event Hub](https://help.sap.com/docs/sap-cloud-application-event-hub/sap-cloud-application-event-hub-service-guide/what-is) in order to subscribe to the described events.

#### `channels`

The `channels` field is REQUIRED. All events that are produced or consumed MUST be assigned to a channel. See [Channels Object](#channels-object) for further details.

#### `components`

The `components` field is REQUIRED. All event MUST be defined under the [`messages`](#messages) field of the Components Object. See [Components Object](#components-object) for further details.

#### `x-sap-catalog-spec-version`

The [`x-sap-catalog-spec-version`](#x-sap-catalog-spec-version-1) extension MUST be provided.

#### `x-sap-application-namespace`

The [`x-sap-application-namespace`](#x-sap-application-namespace-1) extension MUST be provided (`x-sap-catalog-spec-version` `1.2` and higher).

#### `x-sap-ord-id`

The [`x-sap-ord-id`](#x-sap-ord-id-1) extension SHOULD be provided (`x-sap-catalog-spec-version` `1.2` and higher). This ID is defined and known when publishing via ORD to the Business Accelerator Hub or the UCL.

#### `x-sap-stateInfo`

The [`x-sap-stateInfo`](#x-sap-stateinfo-2) extension MUST be provided on catalog level if applicable.

#### `x-sap-shortText`

The [`x-sap-shortText`](#x-sap-shorttext-1) extension MAY be provided.

### [Channels Object](https://www.asyncapi.com/docs/specifications/v2.0.0#channelsObject)

The Channels Object MUST include a [Channel Item Object](#channel-item-object) for every produced or consumed event. The channel name SHOULD reflect the path to which the event is published or it is consumed from. If no real path is available, the CloudEvent `type` MAY be used.

### [Channel Item Object](https://www.asyncapi.com/docs/specifications/2.0.0#channelItemObject)

#### `subscribe`

The `subscribe` field is REQUIRED for every event actively **produced** (please note that the direction here may be counter-intuitive to some).  It uses an [Operation Object](#operation-object).

#### `publish`

The `publish` field MAY be used to indicate that the application itself **consumes** an event (please note that the direction here may be counter-intuitive to some). Is uses an [Operation Object](#operation-object).

### [Operation Object](https://www.asyncapi.com/docs/specifications/2.0.0#operationObject)

#### `message`

The `message` field is REQUIRED. Events MUST NOT be defined directly in the `message` field. Instead, the definition MUST be referenced via `$ref` from the [`messages`](#messages) field of the [Components Object](#components-object).

### [Components Object](https://www.asyncapi.com/docs/specifications/2.0.0#componentsObject)

#### `messages`

The `messages` field is REQUIRED. All events MUST be defined via a [Message Object](#message-object) included in this field.

> Via the `publish` and `subscribe` operations in the [Channel Item Object](#channel-item-object), it is indicated whether this event definition is actively published, consumed, or both.

#### `schemas`

The `schemas` field is OPTIONAL. Primary expected usage is to define [Schema Objects](https://www.asyncapi.com/docs/specifications/2.0.0#schemaObject) describing event data which are referenced from the Message Object's [payload](#payload) field.

### [Message Object](https://www.asyncapi.com/docs/specifications/2.0.0#messageObject)

The Message Object MUST correctly describe an event with event context and event data as mandated by the CloudEvents specification for SAP ecosystem.

> Note: when interpreting the event catalog document, the `$ref` pointers MUST first be dereferenced and only after that the [message traits](#traits) MUST be applied.

#### `name`

The `name` field MUST be set to the same value as the event's `type` context attribute.

#### `headers`

The `headers` field MUST be used to describe the event context attributes.

After dereferencing the `$ref` pointers and applying the [message traits](#traits), the resulting denormalized message:

- MUST define the required context attributes `id`, `source`, `specversion` and `type`.
- SHOULD define the optional context attributes `dataschema`, `datacontenttype`, `subject` and `time`.
- MUST define all used context attributes as exactly as possible through the schema.
  - All context attributes with a fixed value MUST be defined with a `const` value.
    - This MUST include at least `specversion`, `source` and `type`.
    - This is additionally RECOMMENDED for `datacontenttype` (defaults to `application/json` if absent).
  - All context attributes without a fixed value SHOULD be defined with `examples`.
    - This is RECOMMENDED for `id`, `subject` and `time`.
- MUST define all extension context attributes used by the event.
- MUST define the `required` array including all required context attributes.
  - This MUST include at least `id`, `source`, `specversion` and `type`.
  - This MAY include additional (extension) context attributes as determined by the event.

#### `payload`

The `payload` field MUST be used to describe the event data. The event data MAY be defined directly in the `payload` field or the `payload` field MAY reference a [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Component Object's [`schemas`](#schemas) field.

See [Defined JSON Schema Extensions](#defined-json-schema-extensions) for extensions that MAY be used in the Schema Object.

#### `traits`

The `traits` field MAY be used to apply message traits to the event in order to to add groups of attributes that are common to multiple events. This is RECOMMENDED for the standard CloudEvents context attributes of the CloudEvents specification for SAP ecosystem that are common to multiple events in the catalog. See the [Message Trait Object](#message-trait-object) for details and a template for a Message Trait Object defining the standard CloudEvents context attributes.

#### `x-sap-event-spec-version`

The [`x-sap-event-spec-version`](#x-sap-event-spec-version-1) extension MUST be provided by every message (`x-sap-catalog-spec-version` `1.2` and higher).

#### `x-sap-event-source`

The [`x-sap-event-source`](#x-sap-event-source-1) extension MUST be provided (`x-sap-catalog-spec-version` `1.2` and higher).

#### `x-sap-event-source-parameters`

The [`x-sap-event-source-parameters`](#x-sap-event-source-parameters-1) extension MUST be provided (`x-sap-catalog-spec-version` `1.2` and higher).

#### `x-sap-event-characteristics`

The [`x-sap-event-characteristics`](#x-sap-event-characteristics-1) extension SHOULD be provided by every message.

#### `x-sap-stateInfo`

The [`x-sap-stateInfo`](#x-sap-stateinfo-2) extension MUST be provided on message (event) level if applicable.

#### `x-sap-event-version`

The [`x-sap-event-version`](#x-sap-event-version-1) extension MUST be provided if applicable.

### [Message Trait Object](https://www.asyncapi.com/docs/specifications/2.0.0#messageTraitObject)

A Message Trait Object MAY be applied to [Message Objects](#message-object). All attributes of the Message Trait Object are then added to the referencing Message Object.

> **WARNING** :warning:: Please be aware that the inheritance behavior of traits in the AsyncAPI 2.0 specification is potentially counter-intuitive:
> A trait will overwrite everything that is already in the message. The trait is therefore more "specific" than the message it is applied to.
>
> Effectively this means that the trait **can not provide default values**, e.g. via `const`.
> This also for instance affects the definition of `required` headers as the `required` array of trait will replace a `required` array defined in the message header object (see this [issue](https://github.com/asyncapi/asyncapi/issues/505) for more details).

#### `CloudEventsContext.v1` messageTrait Template

The following template MAY be used as a basis to define the standard CloudEvents context attributes.

To use this template, the `examples` and `const` values MUST be adjusted to fit the actual resulting events.
It is RECOMMENDED to add further context attributes that are present in every described event to the `required` attributes.

```json
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

## Defined Specification Extensions

This specification defines additional extensions according to the rules given in [Specification Extensions](https://www.asyncapi.com/docs/specifications/2.0.0#specificationExtensions) of the AsyncAPI 2.0 specification. Defined extensions start with a `x-sap-` prefix. The description of the extensions include their applicability.

To add new SAP defined extensions to this specification, please create a new [issue](https://github.com/SAP/asyncapi-specification/issues/new).

### `x-sap-catalog-spec-version`

- Type: `String`
- Used at: [AsyncAPI Object](#asyncapi-object) (root level)
- Description: Extension that MUST be used to specify which version of this specification the given catalog document uses.

Constraints:

- REQUIRED
- Allowed values are:
  - `1.0`
  - `1.1`
  - `1.2`

### `x-sap-event-spec-version`

- Type: `String`
- Used at: [Message Object](#message-object)
- Description: Extension describing the version of the CloudEvents specification for SAP ecosystem the given event adheres to.

Constraints:

- OPTIONAL for catalogs of [version](#x-sap-catalog-spec-version) `1.0` and `1.1`
- REQUIRED for catalogs of [version](#x-sap-catalog-spec-version) `1.2` and higher
- MUST be a valid CloudEvents specification for SAP ecosystem version when present
  - Defaults to version `1.2` when omitted
- See [`asyncapi.schema.json`](./asyncapi.schema.json) for the exact schema

Examples:

- `"2.0"`

### `x-sap-event-source`

- Type: `String`
- Used at: [Message Object](#message-object)
- Description: Extension that MUST be used to describe the pattern that is used for the `source` attribute.

Constraints:

- OPTIONAL for catalogs of [version](#x-sap-catalog-spec-version) `1.0` and `1.1`
- REQUIRED for catalogs of [version](#x-sap-catalog-spec-version) `1.2` and higher if the `source` may differ between different instances and indicate all variable parts as parameters
- MUST follow the general structure of the `source` attribute and MAY include parameters
- MUST define parameters using a name enclosed in curly brackets (`{}`)

Examples:

- `/default/sap.s4/{instanceId}`
- `/{region}/sap.sf.wa/{instanceId}`

### `x-sap-event-source-parameters`

- Type: `Object`
- Used at: [Message Object](#message-object)
- Description: Extension that MUST be used to describe a map of parameters used in `x-sap-event-source`.

Constraints:

- OPTIONAL for catalogs of [version](#x-sap-catalog-spec-version) `1.0` and `1.1`
- REQUIRED for catalogs of [version](#x-sap-catalog-spec-version) `1.2` and higher if the `source` may differ between different instances and contain all the parameters used in the `x-sap-event-source`
- Keys represent the name of the parameter and MUST match the parameter names used in the `x-sap-event-source`
- MUST only contain parameters of type `string`
- See [`asyncapi.schema.json`](./asyncapi.schema.json) for the exact schema.

Examples:

```json
{
  "region": {
    "description": "The region the application is deployed in.",
    "schema": {
      "type": "string"
    }
  },
  "instanceId": {
    "description": "The system and client of the S/4 application.",
    "schema": {
      "type": "string"
    }
  }
}
```

### `x-sap-object-type`

- Type: `String`
- Used at: [Message Object](#message-object)
- Description: Extension that MAY be used to define that the given event relates to a certain business object or entity.

Constraints:

- OPTIONAL
- MUST NOT be empty if present
- MUST only be used by S/4HANA
- MAY be deprecated in the future in favor of a more general concept

Examples:

- `sap.odm.workforce.WorkforceAvailability`
- `BillOfMaterial`

### `x-sap-odm-version`

- Type: `String`
- Used at: [Message Object](#message-object)
- Description: Extension for the ODM Events Catalog. Specifies the full version of the SAP One Domain Model used for the given physical ODM event definition.

Constraints:

- OPTIONAL
- MUST be the full identifier of the ODM version notated as `String`.
  - pattern `^([0-9]|[1-9][0-9]*)[.]([0-9]|[1-9][0-9]*)[.]([0-9]|[1-9][0-9]*)(-[A-z0-9-]+)?$`

Examples:

- `2.0.0`
- `2.1.0-20201209151056`

### `x-sap-logical-odm-event-version`

- Type: `String`
- Used at: [Message Object](#message-object)
- Description: Extension for the ODM Events Catalog. Specifies the full version of the logical ODM event definition used for the given physical ODM event definition.

Constraints:

- OPTIONAL
- MUST be the full version identifier of the logical ODM event definition notated as `String`
  - pattern `^([0-9]|[1-9][0-9]*)[.]([0-9]|[1-9][0-9]*)[.]([0-9]|[1-9][0-9]*)(-beta([.]([0-9]|[1-9][0-9]*))?)?$`
  
Examples:

- `1.1.0`
- `2.0.0-beta.1`

### `x-sap-event-characteristics`

- Type: `Object`
- Used at: [Message Object](#message-object)
- Description: Extension that is used to specify which event characteristics are present in which variant.

Constraints:

- OPTIONAL
- MUST define key-value pairs where keys denote one of the defined event `characteristics` and values denote one of the characteristic's defined `variants`.
  - The set of `characteristics` and `variants` is defined in the Event Characteristics document.
- MUST NOT change incompatibly (incompatible changes require a major version increment of the event type, see Event Characteristic Compatibility Requirements for details)
  - Adding new characteristics is considered compatible
  - Removing a characteristic is considered INCOMPATIBLE
  - Changing a specified variant to another variant is considered INCOMPATIBLE
- See [`asyncapi.schema.json`](./asyncapi.schema.json) for the exact schema.

Examples:

```json
"x-sap-event-characteristics": {
  "instance-identification": "TO BE DEFINED", 
  "sequencing": "instance-precedence", 
  "state-transfer": "full-after-image" 
}
```

### `x-sap-application-namespace`

- Type: `String`
- Used at: [AsyncAPI Object](#asyncapi-object)
- Description: Extension describing the system namespace (formerly known as application namespace) the producing application / service identifies with.

Constraints:

- OPTIONAL for catalogs of [version](#x-sap-catalog-spec-version) `1.0` and `1.1`
- REQUIRED for catalogs of [version](#x-sap-catalog-spec-version) `1.2` and higher
  - MAY be omitted if the application / service is only consuming events and does not publish any events
- MUST be a registered system namespace in the Namespace Registry
  - for example, if the application / service registered the system namespace `sap.app` including sub-namespaces `sap.app.comp1` and `sap.app.comp2`, the value `sap.app` MUST be given
- All produced events described by the given catalog MUST use a `source` with the given system namespace or a sub-namespace of it
  - for example: `/eu10/sap.app/4711`, `/eu10/sap.app.comp1/4711`, `/eu10/sap.app.comp2/4711`
- See [`asyncapi.schema.json`](./asyncapi.schema.json) for the exact schema

Examples:

- `"sap.s4"`
  - NOT `"sap.s4.beh"`
- `"sap.grc"`
  - NOT `"sap.grc.risk"`

### `x-sap-ord-id`

- Type: `String`
- Format: Valid [ORD ID for Event Resources](https://open-resource-discovery.github.io/specification/spec-v1/interfaces/document#event-resource_ordid)
- Used at: [AsyncAPI Object](#asyncapi-object)
- Description: The ORD ID can be used to lookup more high-level metadata via Business Accelerator Hub or Unified Customer Landscape. It is also used when describing Integration Dependencies to indicate event subscriptions.

Constraints:

- MUST be a valid [ORD ID](https://open-resource-discovery.github.io/specification/spec-v1/#ord-id) that has been used to publish the AsyncAPI document as [Event Resource](https://open-resource-discovery.github.io/specification/spec-v1/interfaces/document#event-resource_ordid).
  - Regexp: `^([a-z0-9]+(?:[.][a-z0-9]+)*):(eventResource):([a-zA-Z0-9._\-]+):(v0|v[1-9][0-9]*)$`

Examples:

- `"sap.billing.sb:eventResource:BusinessEvents_SubscriptionEvents:v1"`
- `"sap.s4:eventResource:BillofMaterialEvents:v1"`

### `x-sap-stateInfo`

- Type: `Object`
- Used at: [AsyncAPI Object](#asyncapi-object) and [Message Object](#message-object)
- Description: Deprecation status of the overall event catalog document or individual event.

Constraints:

- OPTIONAL
  - state defaults to `ACTIVE` if not present
- Schema:
    |Field Name|Type|Description|
    |---|---|---|
    |state|`String`|**REQUIRED**. Indicates the event's / catalog's status. Possible values: `BETA`, `ACTIVE` and `DEPRECATED`. </br></br> The default state is `ACTIVE` and in general it is expected that the `x-sap-stateInfo` is omitted for an active event / catalog. </br></br> There is currently no explicit `DECOMMISSIONED` state because decommissioned events are removed from the catalog. It MAY be introduced in the future however.|
    |deprecationDate|`String`|Deprecation date of the event / catalog (when the deprecation process started). This attribute is RECOMMENDED when an event / catalog is being deprecated. </br></br> The `deprecationDate` must follow the [RFC 3339 full-date](https://datatracker.ietf.org/doc/html/rfc3339#section-5.6) format `yyyy-mm-dd`. For example: `2022-01-01`, `2021-11-14`. </br></br> Consumers should use this date to know they should stop using this deprecated event. The provided date may be used to anticipate the decommission deadline, based on the rules for minimum lifespan.|
    |decommissionedDate|`String`|Decommission date (aka sunset date) of the event / catalog, indicating when it will not be available anymore. This attribute is RECOMMENDED to provide for deprecated events / catalog and MUST be provided as soon as the date is known when and need to be communicated externally. </br></br> The `decommissionedDate` must follow the [RFC 3339 full-date](https://datatracker.ietf.org/doc/html/rfc3339#section-5.6) format `yyyy-mm-dd`. For example: `2022-01-01`, `2021-11-14`.|
    |link|`URL`|Link to release notes or similar with more details and migration instructions.|

Examples:

```json
"x-sap-stateInfo": {
  "state": "DEPRECATED",
  "deprecationDate": "2022-04-30",
  "decommissionedDate": "2023-04-30",
  "link": "https://example.com/release-notes/2204"
}
```

### `x-sap-event-version`

- Type: `String`
- Used at: [Message Object](#message-object)
- Description: Extension describing the full semantic version of the event. Not to be confused with the used version of the CloudEvents specification for SAP ecosystem ([`x-sap-event-spec-version`](#x-sap-event-spec-version-1)).

Constraints:

- MUST be a valid semantic version (`major.minor.patch`) according to the rules described in Event Compatibility, Versioning and Lifecycle.
- OPTIONAL

Examples:

- `"1.2.0"`

### `x-sap-shortText`

- Type: `String`
- Used at: [AsyncAPI Object](#asyncapi-object)
- Description: To display a short description of your events. Displayed by SAP Business Accelerator Hub in tiles.

Constraints:

- OPTIONAL
- REQUIRED for publication at SAP Business Accelerator Hub
- MUST adhere to the constraints defined for `shortDescription` in the ORD Document's [`Event Resource`](https://open-resource-discovery.github.io/specification/spec-v1/interfaces/document#event-resource), including the  [SAP Core Policy Level (v1.0)](https://open-resource-discovery.github.io/specification/spec-extensions/policy-levels/sap-core-v1#short-description-constraints)

### `x-sap-software-min-version`

- Type: `String`
- Used at: [AsyncAPI Object](#asyncapi-object)
- Description: To provide the minimum software version. Mostly addresses on-premise software.

Constraints:

- OPTIONAL

## Defined JSON Schema Extensions

This specification defines additional keywords for the [Schema Object](https://www.asyncapi.com/docs/specifications/v2.0.0#schemaObject) used to describe the [`payload`](#payload) of the [Message Object](https://www.asyncapi.com/docs/specifications/v2.0.0#messageObject).

### `x-key`

- Type: `Array`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload)
- Description: Extension describing the primary identifier of the business object.
- Items:
  - Type: `String`

Constraints:

- Defaults to an empty array when not present
- MUST only be used for messages with content type `application/json`
  - Additional content types MAY be added in the future as seen need
- MUST define a set of attributes that together define the identify of the described object
- MUST NOT define a set of alternative identifiers or their precedence
- Items:
  - MUST be unique names of existing properties of type `String`, `Number` or `Integer` in the root of the described object

Example:

```json5
{
  // ...
  "components": {
    // ...
    "schemas": {
      "sap.s4.beh.SalesOrder.Approved.v1": {
        // ...
        "description": "Payload of the sap.s4.beh.SalesOrder.Approved.v1 event.",
        "type": "object",
        "x-key": ["id"],
        "properties": {
          "id": {
            "type": "string"
          },
          "displayName": {
            "type": "string"
          }
        }
      }
    }
  }
}
```

> Note: this follows the proposal of ["Define 'primary key' for objects"](https://github.com/OAI/OpenAPI-Specification/issues/587) in OpenAPI.

### `x-sap-odm-entity-name`

- Type: `String`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload)
- Description: Name of an ODM entity as a general concept, not a concrete version thereof.
The annotated schema is one of many representations of the ODM entity. Annotating the schema with this term helps consumers find APIs that process or expose the same entity.

Constraints:

- OPTIONAL
- MUST NOT be empty if present
- The value MUST be specified accordingly to the field 'ODM Entity Name' from the catalog of ODM Entities

Example:

```json5
{
  // ...
  "components": {
    // ...
    "schemas": {
      "sap.s4.beh.SalesOrder.Approved.v1": {
        // ...
        "description": "Payload of the sap.s4.beh.SalesOrder.Approved.v1 event.",
        "type": "object",
        "x-sap-odm-entity-name": "CustomerOrder",
        "properties": {
          "id": {
            "type": "string"
          },
          "displayName": {
            "type": "string"
          }
        }
      }
    }
  }
}
```

### `x-sap-odm-oid-reference-entity-name`

- Type: `String`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload)
- Description: An extension to specify the ODM entity name of the referenced entity if required. The extension is to be used on property level and the value must be the valid entity name.

Constraints:

- OPTIONAL
- MUST NOT be empty if present
- The value MUST be specified accordingly to the field 'ODM Entity Name' from the catalog of ODM Entities 

Example:

The `x-sap-odm-oid-reference-entity` custom field points to the ODM entity `ReferencedEntity`.

```json
{
  "components": {
    // ...
    "schemas": {
      "Cat": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int64"
          },
          "referencedEntity": {
            "x-sap-odm-oid-reference-entity-name": "ReferencedEntity",
            "type": "string",
            "maxLength": 128
          }
        }
      }
    }
  }
}
```

### `x-sap-dpp-entity-semantics`

- Type: `String`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload)
- Description: Primary meaning of the personal data in the annotated event. Events annotated with `x-sap-dpp-entity-semantics` are indicating that at least one property of the event data does contain an `x-sap-dpp-is-potentially-personal` annotation.
- Allowed Values:
  - `sap:DataSubject`: Describes a data subject, for example, a customer or vendor.
  - `sap:DataSubjectDetails`: Describes entities that contain details for a data subject, for example an address.
  - `sap:Other`: Entities that contain personal data or references to data subjects but do not represent data subjects or details itself. For example: customer quote, customer order, or purchase order.

Constraints:

- OPTIONAL
- MUST NOT be empty if present
- Describes whether the `<businessObjectName>` declared by the event type and represented in the event data is a dpp relevant entity.

Example:

An example using multiple DPP-related annotations:

```json
{
  // ...
  "components": {
    // ...
    "schemas": {
      "sap.s4.beh.BusinessPartner.Created.v1": {
        // ...
        "description": "Payload of the sap.s4.beh.BusinessPartner.Created.v1 event.",
        "type": "object",
        "x-sap-dpp-entity-semantics": "sap:DataSubject",
        "x-sap-dpp-data-subject-role": "customer",
        "x-sap-dpp-data-subject-role-description": "a direct customer of our enterprise",
        "properties": {
          "id": {
            "type": "string",
            "x-sap-dpp-field-semantics": "sap:DataSubjectID"
          },
          "displayName": {
            "type": "string",
            "x-sap-dpp-is-potentially-personal": true
          },
          "primaryContactName": {
            "type": "string",
            "x-sap-dpp-is-potentially-personal": true
          }
        }
      }
    }
  }
}
```

### `x-sap-dpp-data-subject-role`

- Type: `String`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload)
- Description: Role of the data subject represented in the event data (e.g. employee, customer). Values are application-specific.

Constraints:

- OPTIONAL

### `x-sap-dpp-data-subject-role-description`

- Type: `String`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload)
- Description: Description of the role of the data subject represented in the event data (e.g. employee, customer). Values are application-specific.

Constraints:

- OPTIONAL

### `x-sap-dpp-field-semantics`

- Type: `String`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload) that describes the event data properties. Also referenced by the event's `dataschema`
- Description: Primary meaning of the personal data contained in the value of the annotated property for the event data. Use this annotation also on properties that are already marked as contact or address data. Properties annotated with `x-sap-dpp-field-semantics` SHOULD NOT be additionally annotated with `x-sap-dpp-is-potentially-personal`.
- Allowed Values:
  - `sap:DataSubjectID`: Identifies the data subject unique key or references it.
  - `sap:DataSubjectIDType`: The type describing the DataSubjectID identifying the data subject, e.g., an e-mail address, semantic of the ID or a customer.
  - `sap:ConsentID`: The unique identifier for a consent. A consent is an action of data subjects confirming that the usage of their personal data shall be allowed for a given purpose. A consent functionality allows the storage of a consent record in relation to a specific purpose and shows if a data subject has granted, withdrawn, or denied consent.
  - `sap:PurposeID`: The unique identifier for the purpose of processing of personal data. Any processing of personal data is based on specified, explicit, and legitimate purposes and not further processed in a manner that is incompatible with those purposes. The purpose is defined by the data controller or joint data controllers.
  - `sap:ContractRelatedID`: The unique identifier for transactional data that is related to a contract that requires processing of personal data. For example, Sales Contract ID, Purchase Contract ID, or Service Contract ID.
  - `sap:DataControllerID`: The unique identifier of a legal entity which alone or jointly with others determines the purposes and means of the processing of personal data. The Data Controller is fully responsible (and accountable) that data protection and privacy principles (such as purpose limitation or data minimization), defined in the European General Data Protection Regulation (GDPR) or any other data protection legislation, are adhered to when processing personal data. The DataControllerID succeeds the LegalEntityID.
  - `sap:UserID`: The unique identifier of a user. A user is an individual who interacts with the services supplied by a system.
  - `sap:EndOfBusinessDate`: Defines the end of active business and the start of residence time and retention period. End of business is the point in time when the processing of a set of personal data is no longer required for the active business, for example, when a contract is fulfilled. After it has been reached and a customer-defined residence period has passed, the data is blocked and can only be accessed by users with special authorizations (for example, tax auditors). All fields of type Edm.Date or Edm.DateTimeOffset on which the end of business determination depends should be annotated.
  - `sap:BlockingDate`: Defines a date that marks when the provider of the data will block these. This is the point in time when the processing of a set of personal data is no longer required for the active business, for example, when a contract is fulfilled. After it has been reached, the data is blocked in the source and can only be displayed by users with special authorizations (for example, tax auditors); however, it is not allowed to create/change/copy/follow-up blocked data. Consumers of the data should consider if there is an additional purpose to process the data beyond the defined blocking date
  - `sap:EndOfRetentionDate`: Defines a date that marks when the provider of the data can destroy these. Consumers of the data should consider if there is an additional purpose (or a legal hold) to process the data beyond the defined destruction date.
  
Constraints:

- OPTIONAL
- MUST NOT be empty if present 

### `x-sap-dpp-is-potentially-personal`

- Type: `Boolean`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload) that describes the event data properties. Also referenced by the event's `dataschema`
- Description: Property contains potentially personal data. Properties annotated with `x-sap-dpp-field-semantics` SHOULD NOT be additionally annotated with this extension. Personal data describes any information which is related to an identified or identifiable natural person (data subject). An identifiable person is one who can be identified, directly or indirectly, in particular by a reference to an identifier such as a name, an identification number, location data, an online identifier, or to one or more factors specific to the physical, physiological, genetic, mental, economic, cultural, or social identity of that natural person.

Constraints:

- OPTIONAL
- Default: `true`
- MUST NOT be used if value is `false`
- MUST NOT be combined with `x-sap-dpp-is-potentially-sensitive` for the same property

### `x-sap-dpp-is-potentially-sensitive`

- Type: `Boolean`
- Used at: [Schema Object](https://www.asyncapi.com/docs/reference/specification/v2.0.0#schemaObject) of the Message [`payload`](#payload) that describes the event data properties. Also referenced by the event's `dataschema`
- Description: Property contains potentially sensitive personal data. Sensitive personal data is a category of personal data that needs special handling. The determination which personal data is sensitive may differ for different legal areas or industries. 

  Examples of sensitive personal data: 
    - Special categories of personal data, such as data revealing racial or ethnic origin, political opinions, religious or philosophical beliefs, trade union membership, genetic data, biometric data, data concerning health or sex life or sexual orientation.
    - Personal data subject to professional secrecy. 
    - Personal data relating to criminal or administrative offenses. 
    - Personal data concerning insurances and bank or credit card accounts.

Constraints:

- OPTIONAL
- Default: `true`
- MUST NOT be used if value is `false`
- MUST NOT be combined with `x-sap-dpp-is-potentially-personal` for the same property

## Event Catalog Compatibility, Versioning and Lifecycle

Event catalog documents MUST be treated with the same compatibility, versioning, and lifecycle requirements as API descriptions in general.

### Compatibility and Versioning

The [version](#info) of the event catalog document MUST be incremented according to the semantic versioning changes of the events it includes. The following rules apply:

|Event Change|Impacts on the event catalog document|
|---|---|
|Patch version increment|Patch version increment|
|Minor version increment|Minor version increment|
|Major version increment (new event)|Minor version increment (+ new event in the catalog)|
|Event deprecated|Minor version increment|
|Event decommissioned|Major version increment|

Additionally, the event catalog document version MAY evolve independently from the events according to semantic versioning. Any change not listed in the following is by definition incompatible and requires increasing the MAJOR version of the catalog.

> Note that the list of PATCH and MINOR changes is likely incomplete and MAY be extended based on a case by case discussion with the [authors](#contact-persons) of this specification.

#### Patch Changes

- Changing descriptive texts or documentation
- Editorial changes, e.g., refactoring for re-use elements such as traits, that are NOT changing the resulting effective definition

#### Minor Changes

- Migrating to a greater [`x-sap-catalog-spec-version`](#x-sap-catalog-spec-version-1)
- Adding additional metadata (e.g., optional `x-sap-...` attributes)

#### Major Changes

All other changes not listed in PATCH or MINOR above.

### Lifecycle and Deprecation

The event catalog document remains in ACTIVE state for as long as there is at least one active event included in the catalog. Also:

- The event catalog document transitions to DEPRECATED state when all events of the catalog are deprecated.
- The event catalog document transitions to DECOMMISSIONED state when all events of the catalog are decommissioned.

The lifecycle state of the event catalog document MUST be indicated using the [`x-sap-stateInfo`](#x-sap-stateinfo-2) on [catalog level](#x-sap-stateinfo) extension. The lifecycle of the single events MUST be indicated using the same on the [message level](#x-sap-stateinfo-1).

The application, and therefore SAP Business Accelerator Hub, MUST always expose the event catalog document in its latest version.

## Examples

- A simplified example can be found at [examples/example1.json](./examples/example1.json)
- A realistic example event catalog document from S4 can be found at [examples/s4.json](./examples/s4.json)
  - Please note that this EventCatalog document contains warnings. Each providing system might choose different tradeoffs regarding recommendations.
- An ODM event based example can be found at [examples/odm-example.json](./examples/odm-example.json)
  - An excerpt of the ODM based example with some explanatory comments is shown below

```json5
{
  "asyncapi": "2.0.0",
  "x-sap-catalog-spec-version": "1.2",
  "x-sap-application-namespace": "sap.sf",
  "x-sap-shortText": "Example events to demonstrate the AsyncAPI specification for SAP ecosystem.",
  "info": {
    "title": "Sample AsyncAPI for SAP ecosystem document",
    "version": "1.2.0"
  },
  "channels": {
    "default/sap.sf.wa/1234567/ce/sap/odm/workforce/WorkforceAvailability/Created/v1": {
      "subscribe": {
        "message": {
          // channels message referencing a components message
          // subscribe indicating that this event is actively produced
          "$ref": "#/components/messages/sap.odm.workforce.WorkforceAvailability.Created.v1"
        }
      }
    },
  },
  "components": {
    "messages": {
      // definition of the event
      "sap.odm.workforce.WorkforceAvailability.Created.v1": {
        // extension attributes of the message
        "x-sap-event-version": "1.1.0",
        "x-sap-event-spec-version": "2.0",
        "x-sap-odm-version": "2.1.0",
        "x-sap-logical-odm-event-version": "1.1.0",
        "x-sap-event-source": "/{region}/sap.sf.wa/{instanceId}",
        "x-sap-event-characteristics": {
          "instance-identification": "key-subject", 
          "sequencing": "instance-precedence", 
          "state-transfer": "full-after-image"
        },
        "x-sap-event-source-parameters": {
          "region": {
            "description": "The region the application is deployed in.",
            "schema": {
              "type": "string"
            }
          },
          "instanceId": {
            "description": "The SuccessFactors tenant id.",
            "schema": {
              "type": "string"
            }
          }
        },
        "name": "sap.odm.workforce.WorkforceAvailability.Created.v1",
        "headers": {
          "type": "object",
          "properties": {
            "type": {
              // defining the type standard context attribute according to CloudEvents specification for SAP ecosystem
              "const": "sap.odm.workforce.WorkforceAvailability.Created.v1"
            },
            "source": {
              "const": "/default/sap.sf.wa/1234567"
            },
          }
        },
        "payload": {
          // data defined separately under schemas
          "$ref": "#/components/schemas/sap.odm.workforce.WorkforceAvailability.Created.v1"
        },
        "traits": [
          // using a trait to add further standard context attributes to the event
          {
            "$ref": "#/components/messageTraits/sap.odm.CloudEventsContext.v1"
          }
        ]
      }
    },
    "schemas": {
      "sap.odm.workforce.WorkforceAvailability.Created.v1": {
        // schema for the data of the event
        "type": "object",
        "properties": {
          "id": {
            "type": "string",
            "pattern": "^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$",
            "description": "Entity identifier based on Universally Unique Identifier."
          },
          "workAssignmentId": {
            "type": "string",
            "maxLength": 100,
            "description": "Reference to the work assignment this availability record depends on."
          },
          "availabilityDate": {
            "type": "string",
            "format": "date",
            "description": "Day to which the availability relates."
          }
          // ...
        },
        "required": [
          "id",
          "workAssignmentId",
          "availabilityDate"
          // ...
        ],
        "x-key": [
          "id"
        ],
        "description": "Represent day-wise availability of an employee considering Work Schedule, Holiday Calendar and Temporary Changes. Also, provides absence (time off) information as a composition\naspect. Workforce availability is used for staffing and resource planning of employees."
      }
    },
    "messageTraits": {
      // more specific trait used for ODM events
      "sap.odm.CloudEventsContext.v1": {
        "headers": {
          "type": "object",
          "properties": {
            "id": {
              "description": "Identifies the event.",
              "type": "string",
              "minLength": 1,
              "examples": [
                  "6925d08e-bc19-4ad7-902e-bd29721cc69b"
              ]
            },
            "specversion": {
              "description": "The version of the CloudEvents specification which the event uses.",
              "const": "1.0"
            },
            "subject": {
              "description": "Describes the subject of the event.",
              "type": "string",
              "minLength": 1,
              "maxLength": 256,
              "examples": [
                "ce307052-75a0-4a8f-a961-ebf21669bb80"
              ]
            },
            "sequence": {
              "description": "Identifier expressing the relative order of the event.",
              "type": "string"
            },
            // ...
          },
          "required": [
            "id",
            "source",
            "specversion",
            "type",
            "subject",
            "sequence"
          ]
        }
      }
    }
  }
}
```

## Tools and Resources

- **JSON SCHEMA**
  - A machine-readable JSON schema of the specification is available at [asyncapi.schema.json](./asyncapi.schema.json).
  - It is not possible to express all normative statements of this spec in the schema. The markdown file here remains the source of truth.

## Changelog

See [CHANGELOG.md](./CHANGELOG.md)

## Code of Conduct

We as members, contributors, and leaders pledge to make participation in our community a harassment-free experience for everyone. By participating in this project, you agree to abide by its [Code of Conduct](https://github.com/SAP/.github/blob/main/CODE_OF_CONDUCT.md) at all times.

## Licensing

Copyright 2025 SAP SE or an SAP affiliate company and asyncapi-specification-for-sap-ecosystem contributors. Please see our [LICENSE](LICENSE) for copyright and license information. Detailed information including third-party components and their licensing/copyright information is available [via the REUSE tool](https://api.reuse.software/info/github.com/SAP/asyncapi-specification).
