# # CDS CSN to AsyncAPI specification for SAP ecosystem Mapping - CLI Usage and Options

Base command is:

```sh
cds compile [source] --to asyncapi
```

where:

- `source` can be a single CDS file or a folder containing multiple CDS files

## `--service <'service name'|'all'>`

> Standard option to specify which `service(s)` of the `source` should be included. Either the name of a single service in the `source`, or `all` specifying that all `services` should be included can be given.

The compiler will create a dedicated AsyncAPI document for every service that is to be included. Each individual service MUST have the required annotations (e.g., [`@AsyncAPI.Title`](./metadata-and-presets.md#infotitle), [`@AsyncAPI.Version`](./metadata-and-presets.md#infoversion)). If a mandatory annotation is missing for a service, the compiler will throw an error.

<details>

<summary>
Example
</summary>

### Simplified example

```swift
// file srv/ServiceA.cds
@AsyncAPI.Title: 'Service A Events'
@AsyncAPI.Version: '1.0.0'
service ServiceA {
  // ...
}
```

```swift
// file srv/ServiceB.cds
@AsyncAPI.Title: 'Service B Events'
@AsyncAPI.Version: '2.0.0'
service ServiceB {
  // ...
}
```

#### `cds compile srv --service ServiceA -o docs`

```json5
// file docs/ServiceA.asyncapi2.json
{
  "asyncapi": "2.0.0",
  "info": {
    "title": "Service A Events",
    "version": "1.0.0",
  }
  // ...
}
```

#### `cds compile srv --service all -o docs`

```json5
// file docs/ServiceA.asyncapi2.json
{
  "asyncapi": "2.0.0",
  "info": {
    "title": "Service A Events",
    "version": "1.0.0",
  }
  // ...
}
```

```json5
{
// file docs/ServiceB.asyncapi2.json
  "asyncapi": "2.0.0",
  "info": {
    "title": "Service B Events",
    "version": "2.0.0",
  }
  // ...
}
```

</details>

## `--asyncapi:merged`

AsyncAPI specific flag determining that a single merged AsyncAPI document shall be created. By default, a dedicated AsyncAPI file is created for every `service` of the `source`. It is used in conjunction with the `--service all` option. Using it with `--service <service name>` doesn't make sense and the compiler should fail it.

If the flag `asyncapi:merged` is used, all events from all `services` of the `source` are merged into a single AsyncAPI file. The events are considered active, if they are published or consumed from at least one of the `services`. As `title`, `version` and `description` of the merged document cannot be taken from the annotations of the `services` of the `source` (there may be multiple, how to know which one to choose?), this option is complemented by presets:

```json5
// cdsrc
"cds": {
  "export": {
    "asyncAPI": {
      "merged": {
        "title": "<value>", // REQUIRED, specifies the value for `info.title`
        "version": "<value>", // REQUIRED, specifies the value for `info.version`
        "description": "<value>" // OPTIONAL, specifies the value for `info.description`, default is to omit `info.description`
      }
    }
  }
}
```

The `@AsyncAPI.Extensions` is for now ignored for `services` when the `merged` flag is used. See [ADR16](./adrs.md#adr16-cli-flag-merged-ignored-asyncapiextensions-with-service-as-target) for details.

<details>

<summary>
Example
</summary>

### Simplified example

```swift
// file srv/ServiceA.cds
@AsyncAPI.Title: 'Service A Events'
@AsyncAPI.Version: '1.0.0'
service ServiceA {
  event EventA1 : { key id: String };
  // ...
}
event EventA2 : { key id: String };
```

```swift
// file srv/ServiceB.cds
@AsyncAPI.Title: 'Service B Events'
@AsyncAPI.Version: '2.0.0'
service ServiceB {
  event EventB1 : { key id: String };
  // ...
}
event EventB2 : { key id: String };
```

#### `cds compile srv --service ServiceA --asyncapi:merged`

:exclamation: This is an edge case. `--service <service name> --asyncapi:merged true` together doesn't make sense. Compiler should fail it.

#### `cds compile srv --service all --asyncapi:merged` given no preset available defining merged title and version

:exclamation: This is an error case. Mandatory entries for `title` and `version` must be present in `cdsrc` under `cds.export.asyncapi.merged`.

#### `cds compile srv --service all --asyncapi:merged`

```json5
// cdsrc
"cds": {
  "export": {
    "asyncapi": {
      "merged": {
        "title": "some title",
        "version": "3.0.0",
        "description": "some description"
      }
    }
  }
}
```

Note: only events part of a service are considered.

```json5
{
  "asyncapi": "2.0.0",
  "info": {
    "title": "some title",
    "version": "3.0.0",
    "description": "some description"
  },
  "channels": {
    "EventA1": {
      // ...
    },
    "EventB1": {
      // ...
    }
  },
  "components": {
    "messages": {
      "EventA1": {
        // ...
      },
      "EventB1": {
        // ...
      }
    }
  }
  // ...
}
```

</details>
