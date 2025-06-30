# Specificaties

## Protocollen

## Component: Logboek

### Gedrag

The Logbook **MUST** enforce TLS on connections, in accordance with the standard practice established within the organization.

The Logbook **MUST** confirm the successful persistence of each log entry.

## Interface

The interface **MUST** implement the following fields:

| Veld        | Type    | optioneel | Omschrijving                                                   |
|-------------|---------|---------------|----------------------------------------------------------------|
| `trace_id`  | 16 byte | verplicht     | Unieke identificerende code van Trace die Dataverwerking volgt |
| `span_id`   | 8 byte  | verplicht     | Unieke identificerende code van Actie binnen de Dataverwerking |
| `timestamp` | timestamp   | verplicht     | Unique identifier that refers to an exact instance in time     |
| `request`   | message | verplicht     | Input for the decision in [[AuthZen]] format                   |
| `response`  | message | verplicht     | Output for the decision in [[AuthZen]] format                  |
| `pap`       | message | verplicht     | Zie toelichting                                                |
| `pip`       | message | verplicht     | Zie toelichting                                                |
| `pdp`       | message | verplicht     | Zie toelichting                                                |

Het veld `request` is een `message` die de input voor de toegangsbeslissing representeert. Dit veld **MOET** in AuthZen 
formaat zijn. Je **MAG** delen van het request weg laten vanwege privacy redenen. Wanneer deze delen weg worden gelaten is
het niet meer mogelijk volledige verantwoording en replayability te hebben.

Het veld `response` is een `message` die de input voor de toegangsbeslissing representeert. Dit veld **MOET** in AuthZen
formaat zijn. Je **MAG** delen van het request weg laten vanwege privacy redenen. Wanneer deze delen weg worden gelaten is
het niet meer mogelijk volledige verantwoording en replayability te hebben.

Het veld `PAP` is een `message`, opgebouwd uit de volgende velden.

| Veld             | Type    | optioneel | Omschrijving                         |
|------------------|---------|---------------|--------------------------------------|
| `policy_version` | message | verplicht     | Versie identificatie van de policies |

Het veld `policy_version` is een message die verwijst naar een unieke versie van de policy. De informatie in dit veld **MOET** genoeg
zijn om een specifieke policy terug te halen die gebruikt is in de toegangsbeslissing.

Voorbeelden daarvan zijn:
- Timestamp
- Unieke identifier

Het veld `PIP` is een `message`. 

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