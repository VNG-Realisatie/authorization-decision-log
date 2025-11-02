# Specifications

This section provides the specification for the protocols and interfaces to be used and the expected behavior of the components.

## Protocols

The protocols used between the engine and the log are not prescribed in this standard.

<div class="note">
Note, by "the protocols" we mean the method of delivering messages between components. This standard does describe the interfaces of the messages themselves, which the components **MUST** comply with, to ensure interoperability between component functionalities. The standard does not prescribe how that information is passed between components, as this depends on the technical/architectural choices made by software developers. This provides the freedom to add the standard to almost any software solution.
</div>

It is **RECOMMENDED** to use the OpenTelemetry Protocol (OTLP) for the interaction between the Application and the log.

<div class="note">
OpenTelemetry is a standard and open-source framework for managing, generating, collecting, and exporting telemetry data. Using this open standard can prevent vendor-specific integrations. OpenTelemetry is a CNCF incubating project.
</div>


## Behavior

The log **MUST** enforce TLS on connections, in accordance with the standard practice established within the organization.

The log **SHOULD** confirm the successful persistence of each log entry. If a confirmation is not provided, historical decisions may end up not being logged.

## Interface

The interface **MUST** implement the following fields:

| Field          | Type      | Required  | Description                                                      |
|----------------|-----------|-----------|------------------------------------------------------------------|
| `timestamp`    | timestamp | required  | The exact point in time when the authorization decision was made |
| `request_type` | string    | required  | The type of request as defined in the [[AuthZen]] standard       |
| `request`      | object    | required  | Input for the decision in [[AuthZen]] format                     |
| `response`     | object    | required  | Output of the decision in [[AuthZen]] format                     |
| `policies`     | object    | required  | Policy sources that affected the decision                        |
| `information`  | object    | optional  | Information sources used in the decision                         |
| `engine`       | object    | optional  | Policy Decision Point configuration and metadata                 |

The interface **MUST** have fields that can identify the request. 

It is **RECOMMENDED** to use [[trace-context]] to identify requests by implementing the following fields:

| Field          | Type      | Required  | Description                                                    |
|----------------|-----------|-----------|----------------------------------------------------------------|
| `trace_id`     | 16 byte   | required  | Unique identifier of trace that follows data processing        |
| `span_id`      | 8 byte    | required  | Unique identifier of span within the data processing           |

Implementations **MAY** include additional fields. Such fields **MUST NOT** alter the semantics of the defined fields and **SHOULD** be ignored by recipients that do not recognize them.

### Example (non-normative)

In the following example a manager called Alice attempts to approve a holiday request for a team member called Bob, but the request is denied because she is does not have signing authority. 

Her browser submits the following request to the API:

<aside class="example" title="HTTP request for holiday approval">

```http
POST /users/bob/holiday-requests/446epbc8y7 HTTP/1.1
Host: hr.example.com
Authorization: Bearer <alice-oauth-token>
traceparent: 00-28dbeec32e77635cc19bc3204ec56c41-dec5220770f8f4f4-01

{"action":"approve"}
```
</aside>

The application, acting as the PEP, then submits the following HTTP request to the PDP:

<aside class="example" title="HTTP request from the PEP to the PDP">

```http
POST /access/v1/evaluation HTTP/1.1
Host: pdp.example.com
Content-Type: application/json
Authorization: Bearer <pep-oauth-token>
traceparent: 00-28dbeec32e77635cc19bc3204ec56c41-893e1b2ac52d712f-01

{
	"subject": {
		"type": "user",
		"id": "alice@example.com"
	},
	"action": {
		"name": "approve"
	},
	"resource": {
		"type": "holiday-request",
		"id": "446epbc8y7",
		"properties": {
			"employee": "bob@example.com"
		}
	},
	"context": {
		"traceparent": "00-28dbeec32e77635cc19bc3204ec56c41-dec5220770f8f4f4-01"
	}
}
```
</aside>

The PDP validates these requests against a set of policies for the HR application. These are stored in a Git repository and the git hash of the version currently deployed together with the PDP is `e4c15a063048367da367d5588d703b5e4a6b760e`.

Based on these policies it is determined that the request can only be performed by employees with signatory rights. This signatory information is then requested from a PIP called "managers-api" which results in the following HTTP request:

<aside class="example" title="HTTP request to the Managers API PIP">

```http
GET /users/alice?fields=can_sign HTTP/1.1
Host: hr.example.com
traceparent: 00-28dbeec32e77635cc19bc3204ec56c41-836ff5286112f460-01
```
</aside>

Given that alice is *not* a signatory for the organization the request is denied.

The following JSON object contains a non-normative example of a log record describing this example:

<aside class="example" title="Log record of denied holiday approval">

```json
{
	"timestamp": "2025-09-07T10:14:18Z",
	"trace_id": "28dbeec32e77635cc19bc3204ec56c41",
	"span_id": "893e1b2ac52d712f",
	"request_type": "evaluation",
	"request": {
		"subject": {
			"type": "user",
			"id": "alice@example.com"
		},
		"action": {
			"name": "approve"
		},
		"resource": {
			"type": "holiday-request",
			"id": "446epbc8y7",
			"properties": {
				"employee": "bob@example.com"
			}
		},
		"context": {
			"traceparent": "00-28dbeec32e77635cc19bc3204ec56c41-dec5220770f8f4f4-01"
		}
	},
	"response": {
		"decision": false,
		"context": {
			"reason": {
				"48": "No signing authority"
			}
		}
	},
	"policies": {
		"hr": "e4c15a063048367da367d5588d703b5e4a6b760e"
	},
	"information": {
		"managers-api": "836ff5286112f460"
	}
}
```
</aside>


### Timestamp
The `timestamp` field represents the exact point in time when the authorization decision was made. The timestamp **SHOULD** be in [[RFC3339]] format to ensure consistent interpretation across different systems and regions.

### Request Type
The `request_type` field represents the type of request that was made. This value identifies the AuthZEN endpoint that was invoked. 

Its value **MUST** be a string containing the key value of the relevant endpoint as defined in "Endpoint Parameters" of the "Policy Decision Point Metadata" as defined in [[AuthZEN]] with the `_endpoint` suffix omitted. 

For example, a request to the URL defined by the `search_subject_endpoint` in the PDP metadata would have the `request_type` of `search_subject`.

### Request
The `request` field is an object that represents the input to the decision. This field **MUST** be in [[AuthZen]] format as defined for the given request type. 

Portions of the request **MAY** be omitted for privacy reasons. If information is omitted, this omission **SHOULD** be documented or indicated in the log record. If the omitted information was used by the Policy Decision Point then full accountability can no longer be provided. 

### Response
The `response` field is an object that represents the output of the decision. This field **MUST** be in [[AuthZen]] format as defined for the given request type. 

Portions of the response **MAY** be omitted for privacy reasons. If information is omitted, this omission **SHOULD** be documented or indicated in the log record. If information that was used by the Policy Enforcement Point is omitted then full accountability can no longer be provided.

### Policy Sources

The `policies` field represents a versioned reference to the policies that the PDP used to evaluate the request. In a PxP architecture this represents the information that would come from the Policy Administration Point (PAP).

A PDP can have one or more sources of policies which can be individually versioned. To accommodate that the `policies` field is an object in which each key identifies a specific, versioned, policy source. 

All policy sources that have affected the decision **MUST** be included. The value associated with each key refers to a unique version of the policy source. The information in this field **MUST** be sufficient to retrieve all policies from the policy sources that were used in the authorization decision.

Non-normative examples include:
- Timestamp
- Unique identifier
- Semantic version
- Git hash

The following example shows an HR application that uses a source called "hr" referring to a specific Git commit hash in its policy repository:

<aside class="example" title="Git-versioned policy source">

```json
{
	"hr": "6266d07750c44b4c9b05d0801b752c0ef884e4f6"
}
```
</aside>

More complex references can be achieved by using an object as the version identifier. If, for example, the HR application takes part in a federation with predefined policies for different maturity levels. The following non-normative example shows how those policies can be referenced using a semantic version comined with a filter for policies relevant to the current maturity level.

<aside class="example" title="Complex policy source reference">

```json
{
	"hr": "6266d07750c44b4c9b05d0801b752c0ef884e4f6",
	"federation": {
		"version": "2.7.1",
		"filter": "maturity_level <= 3"
	}
}
```
</aside>


### Information Sources

The `information` field represents all the supporting information used in the evaluation of the access decision. In a PxP architecture this field represents the information that would comes from Policy Information Points (PIPs).

It is an object in which each key identifies an information source. All information sources that have affected the decision **SHOULD** be included. The value of this field **SHOULD** either contain the information that was used in the access decision or be sufficient to retrieve the information. 

In case the information field contains an identifier that is used to retrieve the information, one of the following common source identifiers **MAY** be used:

### Engine

The `engine` field is an object containing the relevant configuration parameters required to recreate the state of the engine that evaluates decisions. 

In a PxP architecture this primarily represents the configuration of the Policy Decision Point (PDP), but MAY also include configuration of Policy Information Points (PIPs) and Policy Administration Points (PAPs).

This allows for replaying historical decisions using the exact software configuration used in the original decision.

Non-normative examples include:
- Type of policy engine
- Version of the policy engine
- Identifier or hostname of the engine in case multiple engines are used
- Configuration of the policy engine

### Trace ID
The `trace_id` field is a 16-byte unique identifier that represents the trace context for the data processing operation. This field **SHOULD** follow the W3C Trace Context specification. The trace ID enables correlation of the authorization decision with related operations.

### Span ID
The `span_id` field is an 8-byte unique identifier that represents the specific span within the trace context. This field **SHOULD** follow the W3C Trace Context specification. The span ID identifies the particular operation or step within the data processing flow where the authorization decision was made.

## Sources and referencing

While policy and information sources MAY be included in the log directly, this is undesirable in most cases as it introduces duplication, increases the size of the log and can result in sensitive data included in the log.

To address we describe several methods of referencing sources from the log below.

### Versioned source

In case the source of information offers the ability to 'time-travel' by providing a version at which to query then the data itself may be omitted.  

The version identifier can be a simple value, such as a string or number, or a complex object, such as an array or object containing multiple version identifiers.

The following non-normative example shows a reference to a specific semantic version of a policy source.

<aside class="example json" title="Policy source reference using semantic versioning">
{
	"traffic-policy": "gmb-2025-94604@1.1"
}
</aside>

In a complex case, such as limiting open data requests per IP across a large number of servers, the version could also consist of an array of partition offsets in a Kafka stream of HTTP request.

<aside class="example json" title="Complex versioned information sources using Kafka partition offsets">
{
	"nginx-requests": [ 8376912, 8368118, 8377785, 8386285, 8383526 ]
}
</aside>

### Temporal source

In case the source of information offers the ability to 'time-travel' by providing a timestamp at which to query, then the data itself may be omitted. 

It is **RECOMMENDED** to use the timestamp defined in the `time` field in the `context` of the `request` as the base time. In that case the information source **MAY** be omitted fully. 

If a different timestamp is used then it **SHOULD** be included in [[RFC3339]] format.

<p class="note" title="Inter-system clock inconsistencies">
When system clocks are not aligned properly, a system may be asked to provide information for a time-stamp that lies in the future. This can be mitigated by requesting the policies of a few seconds or minutes ago at the expense of reducing the speed with which policy changes can be deployed.
</p>

<p class="note" title="Usage of REST API Design Rules">
In the context of REST APIs developed by the Dutch government the <a href="https://docs.geostandaarden.nl/api/API-Strategie-ext/#temporal">Temporal extension</a> of the [[REST API Design Rules]] can be used for this purpose.
</p>

### Logged source

For information sources that are logged in an external log the request to the information source *SHOULD* use the W3C Trace Context standard in the following format. 

It is *RECOMMENDED* to log requests in the [[WARC]] format as it includes all request and response headers that may be used in the authorization decision.

| Field      | Type    | Required | Description                                           |
|------------|---------|----------|-------------------------------------------------------|
| `trace_id` | 16 byte | optional | The trace_id of the request to the information source |
| `span_id`  | 8 byte  | required | The span_id of the request to the information source  |

In case the `trace_id` is identical to that of the main request the `trace_id` **MAY** be omitted.

The following non-normative example shows a log record for a request to find all subjects capable of approving a holiday request:

<aside class="example" title="Log record of a search request for managers with approval rights">

```json
{
	"timestamp": "2025-09-07T10:15:36Z",
	"trace_id": "28dbeec32e77635cc19bc3204ec56c41",
	"span_id": "17c59821784ee492",
	"request_type": "search_subject",
	"request": {
		"subject": {
			"type": "user"
		},
		"action": {
			"name": "approve"
		},
		"resource": {
			"type": "holiday-request",
			"id": "446epbc8y7",
			"properties": {
				"employee": "bob@example.com"
			}
		}
	},
	"response": {
		"results": [
			{
				"type": "user",
				"id": "carol@example.com"
			},
			{
				"type": "user",
				"id": "dan@example.com"
			}
		]
	},
	"policies": {
		"git": "e4c15a063048367da367d5588d703b5e4a6b760e"
	},
	"information": {
		"managers-api": "45deb36022f53afa"
	}
}
```
</aside>

Which would result in the following WARC entries logging the REST API call to the HR system:

<aside class="example" title="WARC entries for REST API call to HR system">

```warc
WARC/1.1
WARC-Type: request
WARC-Date: 2025-09-11T13:55:01Z
WARC-Record-ID: <urn:uuid:48bafbce-8d2a-45c1-9d7a-1a851c36e1c8>
Content-Type: application/http; msgtype=request
Content-Length: 142

GET /users/bob/managers?fields=can_sign HTTP/1.1
Host: hr.example.com
traceparent: 00-28dbeec32e77635cc19bc3204ec56c41-45deb36022f53afa-01

WARC/1.1
WARC-Type: response
WARC-Date: 2025-09-11T13:55:01Z
WARC-Record-ID: <urn:uuid:4a381180-21a7-4712-8706-5b321c17e3f8>
WARC-Concurrent-To: <urn:uuid:48bafbce-8d2a-45c1-9d7a-1a851c36e1c8>
Content-Type: application/http; msgtype=response
Content-Length: 175

HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 107

{
	"alice": {
		"can_sign": false
	},
	"carol": {
		"can_sign": true,
	},
	"dan": {
		"can_sign": true
	}
}
```
</aside>