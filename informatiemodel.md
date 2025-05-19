# Informatiemodel

## Input en Output

- Opslaan van communicatie PEP -> PDP (input) en PDP -> PEP (output)
- Mogelijk dataminimalisatie

## PAP Versioning

- MUST hebben van versioning in je policies (PAP)
- Hoe verwijzen we naar een specifieke versie
- Is een timestamp genoeg of hebben we meer nodig (git hash)

## PIP Data

- Caching (nd_cache)
  - Technisch makkelijk
  - Mogelijk ongewenste duplicatie van data
- Trace en aparte log
  - Gebruik maken van WARC standaard?
- Bron met historie
  - Alleen identifier en timestamp/version op slaan
  - DSO/REST API Design Rules herbruiken

## PDP

- Mogelijk alleen met timestamp?
- Hoe ga je om met een PDP die alleen versie geeft van policy die permit/deny doet? Versus PDP's waarbij je exact de samenhang van alle policies moet weten?