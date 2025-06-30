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
| `span_id`      | 8 byte    | required | Unieke identificerende code van Actie binnen de Dataverwerking |
| `timestamp`    | timestamp | required | Unique identifier that refers to an exact instance in time     |
| `request`      | message   | required | Input for the decision in [[AuthZen]] format                   |
| `request_type` | string    | required | Type of the request as defined in the [[AuthZen]] standard     |
| `response`     | message   | required | Output for the decision in [[AuthZen]] format                  |
| `policies`     | message   | required | Refer to the notes below                                       |
| `pip`          | message   | required | Refer to the notes below                                 |
| `pdp`          | message   | required | Refer to the notes below                                 |

### Notes
Het veld `request` is een `message` die de input voor de toegangsbeslissing representeert. Dit veld **MOET** in AuthZen 
formaat zijn. Je **MAG** delen van het request weg laten vanwege privacy redenen. Wanneer deze delen weg worden gelaten is
het niet meer mogelijk volledige verantwoording en replayability te hebben.

Het veld `response` is een `message` die de input voor de toegangsbeslissing representeert. Dit veld **MOET** in AuthZen
formaat zijn. Je **MAG** delen van het request weg laten vanwege privacy redenen. Wanneer deze delen weg worden gelaten is
het niet meer mogelijk volledige verantwoording en replayability te hebben.

The `PAP` field is a message, composed of the following fields.

| Veld             | Type    | optioneel | Omschrijving                               |
|------------------|---------|---------------|--------------------------------------------|
| `policy_version` | message | required     | Object to uniquely identify a set policies |

The `policy_version` field is a message that refers to a unique version of the policy. The information in this field **MUST** 
be sufficient to retrieve the specific policy or policy set that was used in the access decision.

Examples include:
- Timestamp
- Unique identifier
- Git hash

The `PAP` field is a message.

| Veld       | Type    | optioneel | Omschrijving                                                   |
|------------|---------|-----------|----------------------------------------------------------------|
| `trace_id` | 16 byte | verplicht | Versie identificatie van de policies                           |
| `span_id`  | 8 byte  | verplicht | Unieke identificerende code van Actie binnen de Dataverwerking |
| `context`  | message | optioneel | Data van de PIP in WARC formaat                                |
| `timestamp` | timestamp (ms)  | verplicht     | Unieke identificerende code van Actie binnen de Dataverwerking |

Beschrijf hier de verschillende strategieen:
- Alles dupliceren wanneer er geen gevoelige data aanwezig
  - Twee soorten data:
    - Gerepliceerde data: Gebruik exacte versies (bijv. versie op de PIP).
    - Live opgehaalde data: Voeg trace ID toe.

Het veld `PDP` is een `message`.

| Veld            | Type    | optioneel | Omschrijving                         |
|-----------------|---------|-----------|--------------------------------------|
| `policies`      | message | verplicht | Versie identificatie van de policies |
| `configuration` | message | optioneel | Configuratie informatie voor de PDP  |

Het veld `policy_version` is een message die de configuratie van de PDP beschrijft. Voorbeelden hiervan zijn:

- Policy engine
- Versie van de policy engine