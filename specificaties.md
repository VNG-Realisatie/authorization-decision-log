# Specifications

## Protocols

## Component: Decision Log

### Behaviour

The Logbook **MUST** enforce TLS on connections, in accordance with the standard practice established within the organization.

The Logbook **MUST** confirm the successful persistence of each log entry.

## Interface

The interface **MUST** implement the following fields:

| Field           | Type    | Required  | Description                                        |
|----------------|-----------|-----------|----------------------------------------------------------------|
| `trace_id`     | 16 byte   | required  | Unieke identificerende code van Trace die Dataverwerking volgt |
| `span_id`      | 8 byte    | required  | Unieke identificerende code van Actie binnen de Dataverwerking |
| `timestamp`    | timestamp | required  | Unique identifier that refers to an exact instance in time     |
| `request`      | message   | required  | Input for the decision in [[AuthZen]] format                   |
| `request_type` | string    | required  | Type of the request as defined in the [[AuthZen]] standard     |
| `response`     | message   | required  | Output for the decision in [[AuthZen]] format                  |
| `policies`     | message   | required  | Refer to the notes below                                       |
| `information`  | message   | optional  | Refer to the notes below                                 |
| `pdp`          | message   | optional  | Refer to the notes below                                 |

### Request
The `request` field is a message that represents the input to the decision. This field **MUST** be in [[AuthZen]] format. Portions 
of the request **MAY** be omitted for privacy reasons. However, if such parts are omitted, full accountability and 
replayability can no longer be guaranteed.

### Response
The `response` field is a message that represents the output of the decision. This field **MUST** be in [[AuthZen]] format. Portions
of the request **MAY** be omitted for privacy reasons. However, if such parts are omitted, full accountability and
replayability can no longer be guaranteed.

### Policies
The `policies` field is a message in which each key identifies a policy source. All policies sources that have affected the decision **MUST** be included.  The value associated with each key refers to a unique version of the policy. The information in this field **MUST** 
be sufficient to retrieve the specific policy or policy set that was used in the access decision.

Examples include:
- Timestamp
- Unique identifier
- Semantic version
- Git hash

### Information
The `information` field is a message in which each key identifies an information source. The information in this field **SHOULD** 
be sufficient to retrieve the information that was used in the access decision.

#### Temporal source
For temporal sources the timestamp used to query the source should be the timestamp defined in the `time` field in the `context` of the `request`. In such cases it *MAY* be omitted from the `information` message. If a different timestamp is used the timestamp **MUST** be a message in the following format.

| Field           | Type    | Required  | Description                                            |
|------------|-----------|-----------|------------------------------------------------------------|
| `timestamp` | timestamp | optional | String value that refers to an exact instance in time | |

#### Logged request

For information sources which are logged in an external log the request to the information source *SHOULD* use the W3C Trace Context standard in the following format. It is *RECOMMENDED* to log such requests in the WARC standard.

| Field           | Type    | Required  | Description                                            |
|------------|-----------|-----------|------------------------------------------------------------|
| `trace_id` | 16 byte   | optional  | The trace_id of the request to the information source |
| `span_id`  | 8 byte    | optional | The span_id of the request to the information source | |

### Policy Decision Point

The `pdp` field is a message.

| Field           | Type    | Required  | Description                          |
|-----------------|---------|-----------|--------------------------------------|
| `version`       | message | optioneel | Version identification of the PDP    |
| `hostname`      | message | optioneel | Hostname of the PDP                  |
| `configuration` | message | optioneel | Configuration information of the PDP |

The `configuration` field is a message that describes the configuration of the PDP. Examples include:
- Policy engine
- Version of the policy engine

Implementations **MAY** include additional fields in the message. Such fields **MUST NOT** alter the semantics of the 
defined fields and **SHOULD** be ignored by recipients that do not recognize them.
