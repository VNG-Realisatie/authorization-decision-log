# Specificaties

## Protocollen

## Component: Logboek

### Gedrag

The Logbook **MUST** enforce TLS on connections, in accordance with the standard practice established within the organization.

The Logbook **MUST** confirm the successful persistence of each log entry.

## Interface

The interface **MUST** implement the following fields:

| Veld           | Type      | optioneel | Omschrijving                                                   |
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

### Notes
Het veld `request` is een `message` die de input voor de toegangsbeslissing representeert. Dit veld **MOET** in AuthZen 
formaat zijn. Je **MAG** delen van het request weg laten vanwege privacy redenen. Wanneer deze delen weg worden gelaten is
het niet meer mogelijk volledige verantwoording en replayability te hebben.

Het veld `response` is een `message` die de input voor de toegangsbeslissing representeert. Dit veld **MOET** in AuthZen
formaat zijn. Je **MAG** delen van het request weg laten vanwege privacy redenen. Wanneer deze delen weg worden gelaten is
het niet meer mogelijk volledige verantwoording en replayability te hebben.

The `policies` field is a message, composed of the following fields.

| Field            | Type    | optioneel | Omschrijving                               |
|------------------|---------|---------------|--------------------------------------------|
| `policy_version` | message | required     | Object to uniquely identify a set policies |

The `policy_version` field is a message that refers to a unique version of the policy. The information in this field **MUST** 
be sufficient to retrieve the specific policy or policy set that was used in the access decision.

Examples include:
- Timestamp
- Unique identifier
- Git hash



The `information` field is a message.

| Veld       | Type      | optioneel | Omschrijving                                               |
|------------|-----------|-----------|------------------------------------------------------------|
| `trace_id` | 16 byte   | optional  | The trace_id of the PDP request                            |
| `span_id`  | 8 byte    | optional | The span_id of the PDP request                             | |
| `timestamp` | timestamp | optional | Unique identifier that refers to an exact instance in time | |
| `context`  | message   | optional | Data of the PIP request                                    | |

The `information` **MUST** contain a `trace_id` and `span_id` or a `timestamp` or `data` field.

The `timestamp` is the timestamp of the request if the `information`.

The `pdp` field is a message.

| Veld            | Type    | optioneel | Omschrijving                         |
|-----------------|---------|-----------|--------------------------------------|
| `version`       | message | optioneel | Version identification of the PDP    |
| `hostname`      | message | optioneel | Hostname of the PDP                  |
| `configuration` | message | optioneel | Configuration information of the PDP |

The `configuration` field is a message that describes the configuration of the PDP. Examples include:
- Policy engine
- Version of the policy engine

Implementations **MAY** include additional fields in the message. Such fields **MUST NOT** alter the semantics of the 
defined fields and **SHOULD** be ignored by recipients that do not recognize them.