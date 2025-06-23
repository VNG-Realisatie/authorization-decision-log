# Architectuur

De decision log komt vanuit het PDP

Drie inputs:
- PEP verzoek als _perceived by the application_
- PAP toegangsbeleid
- PIP verrijking van de data

## Context

Toevoegen architectuur afbeeldingen

## Componenten

### PDP

### PAP

### PIP

### PEP

## Scope

In deze sectie wordt de scope van de standaard afgebakend.

### Geen specificatie voor het beheren van Logboeken
De standaard specificeert een interface voor het wegschrijven van Logregels. Dit is het deel dat in alle organisaties hetzelfde moet zijn om interoperabel te zijn. Het beheren van een Logboek is vrij in te vullen per implementatie.

Dit betekent o.a. dat de standaard geen gedrag of interfaces specificeert voor:

- het verwijderen of muteren van Logregels
- het regelen van toegang tot het Logboek
- het regelen van duurzame toegankelijkheid
- het regelen van archivering en verwijdering van Logregels

## Flows

### Wegschrijven van een logregel na een toegangsbeslissing