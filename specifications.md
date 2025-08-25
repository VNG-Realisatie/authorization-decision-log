# Specifications

This section provides the specification for the protocols and interfaces to be used and the expected behavior of the components.

## Protocols

The protocols used between the engine and the logbook are not prescribed in this standard.

<div class="note">

Note, by "the protocols" we mean the method of delivering messages between components. This standard does describe the interfaces of the messages themselves, which the components **MUST** comply with, to ensure interoperability between component functionalities. The standard does not prescribe how that information is passed between components, as this depends on the technical/architectural choices made by software developers. This provides the freedom to add the standard to almost any software solution.

</div>

It is **RECOMMENDED** to use the OpenTelemetry Protocol (OTLP) for the interaction between the Application and the Logbook.

<div class="note">

OpenTelemetry is a standard and open-source framework for managing, generating, collecting, and exporting telemetry data. Using this open standard can prevent vendor-specific integrations. OpenTelemetry is a CNCF incubating project.

</div>


## Behaviour

The Logbook **MUST** enforce TLS on connections, in accordance with the standard practice established within the organization.

The Logbook **SHOULD** confirm the successful persistence of each log entry. If a confirmation is not provided or required historical decisions may end up not being logged.

## Interface

The interface **MUST** implement the following fields:

| Field          | Type      | Required  | Description                                                    |
|----------------|-----------|-----------|----------------------------------------------------------------|
| `timestamp`    | timestamp | required  | Unique identifier that refers to an exact instance in time     |
| `request_type` | string    | required  | Type of the request as defined in the [[AuthZen]] standard     |
| `request`      | message   | required  | Input for the decision in [[AuthZen]] format                   |
| `response`     | message   | required  | Output of the decision in [[AuthZen]] format                   |
| `policies`     | message   | required  | Policy sources that affected the decision                      |
| `information`  | message   | optional  | Information sources used in the decision                       |
| `engine`          | message   | optional  | Policy Decision Point configuration and metadata               |

The interface **MUST** have fields that can identify the request. 

It is **RECOMMENDED** to use [[trace-context]] to identify requests by implementing the following fields:

| Field          | Type      | Required  | Description                                                    |
|----------------|-----------|-----------|----------------------------------------------------------------|
| `trace_id`     | 16 byte   | required  | Unique identifier of trace that follows data processing        |
| `span_id`      | 8 byte    | required  | Unique identifier of span within the data processing           |

Implementations **MAY** include additional fields in the message. Such fields **MUST NOT** alter the semantics of the 
defined fields and **SHOULD** be ignored by recipients that do not recognize them.

### Timestamp
The `timestamp` field represents the exact point in time when the authorization decision was made. The timestamp **SHOULD** be in [[RFC3339]] format to ensure consistent interpretation across different systems and regions.

### Request Type
The `request_type` field represents the type of authorization decision which was made. Its value **MUST** be a string containing the path of the HTTPS Binding as defined in [[AuthZEN]].

### Request
The `request` field is a message that represents the input to the decision. This field **MUST** be in [[AuthZen]] format as defined for the given request type. 

Portions of the request **MAY** be omitted for privacy reasons. If information that was used by the Policy Decision Point is omitted then full accountability can no longer be provided.

### Response
The `response` field is a message that represents the output of the decision. This field **MUST** be in [[AuthZen]] format as defined for the given request type. 

Portions of the response **MAY** be omitted for privacy reasons. If information that was used by the Policy Enforcement Point is omitted then full accountability can no longer be provided.

### Policies
The `policies` field is a message in which each key identifies a policy source. All policies sources that have affected the decision **MUST** be included. The value associated with each key refers to a unique version of the policy source. The information in this field **MUST** 
be sufficient to retrieve all policies from the policy source that were used in the authorization decision.

Examples include:
- Timestamp
- Unique identifier
- Semantic version
- Git hash

### Information
The `information` field is a message in which each key identifies an information source. The value of this field **SHOULD** contain the information that was used in the access decision or be sufficient to retrieve the information. 

In case the information field contains an identifier that is used to retrieve the information; one of the following common source identifiers **MAY** be used.

#### Temporal source

In case the source of information offers the ability to 'time-travel' by providing a timestamp at which to query then the data itself may be omitted.  

It is **RECOMMENDED** to use use the timestamp defined in the `time` field in the `context` of the `request` for this purpose. In that case the information source **MAY** be omitted fully. 

If a different timestamp is used then the timestamp **SHOULD** be a message in the following format:

| Field           | Type    | Required  | Description                                            |
|------------|-----------|-----------|------------------------------------------------------------|
| `timestamp` | timestamp | optional | String value that refers to an exact instance in time | |

#### Logged request

For information sources which are logged in an external log the request to the information source *SHOULD* use the W3C Trace Context standard in the following format. 

It is *RECOMMENDED* to log requests in the [[WARC]] format as this standard includes all relevant request and response headers which may be used in the authorization decision.

| Field           | Type    | Required  | Description                                            |
|------------|-----------|-----------|------------------------------------------------------------|
| `trace_id` | 16 byte   | optional  | The trace_id of the request to the information source |
| `span_id`  | 8 byte    | required | The span_id of the request to the information source | |

In case the `trace_id` is identical to that of the main request the `trace_id` **MAY** be omitted.

### Engine (Policy Decision Point)

The `engine` field is a message containing the relevant configuration parameters required to recreate the state of the engine (Policy Decision Point) the evaluated the authorization decision. This allows replaying of historical decision using the exact software configuration that evaluated the original decision.

| Field           | Type    | Required  | Description                             |
|-----------------|---------|-----------|-----------------------------------------|
| `version`       | message | optioneel | Version identification of the engine    |
| `hostname`      | message | optioneel | Hostname of the engine                  |
| `configuration` | message | optioneel | Configuration information of the engine |

The `configuration` field is a message that describes the configuration of the engine (PDP). Examples include:
- Type of policy engine
- Version of the policy engine

Additional fields may be included in the message.

### Trace ID
The `trace_id` field is a 16-byte unique identifier that represents the trace context for the data processing operation. This field **SHOULD** follow the W3C Trace Context specification. The trace ID enables correlation of the authorization decision with related operations.

### Span ID
The `span_id` field is an 8-byte unique identifier that represents the specific span within the trace context. This field **SHOULD** follow the W3C Trace Context specification. The span ID identifies the particular operation or step within the data processing flow where the authorization decision was made.

