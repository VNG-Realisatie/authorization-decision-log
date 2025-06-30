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

### No specification for the management of logs
The specification defines an interface for persisting log entries. This is the component that MUST be consistent across organizations to ensure interoperability. The management of a Logbook, however, is left to the discretion of individual implementations.
Consequently, the specification does NOT define behavior or interfaces for:
- deleting or modifying log entries
- managing access to the Logbook
- ensuring long-term accessibility
- handling archiving or deletion of log entries

## Flows

### Wegschrijven van een logregel na een toegangsbeslissing