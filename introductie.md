# Introduction

## Purpose of this standard
Doel van de standaard is het op uniforme wijze vastleggen van informatie die het mogelijk toegangsbeslissingen te verantwoorden.

_Toevoegen replayability aan het doel?_

### Werkingsgebied van de standaard
Functioneel toepassingsgebied: De standaard kan worden toegepast voor iedere aanroep van een API.

Organisatorisch toepassingsgebied: Nederlandse overheden (Rijk, provincies, gemeenten en waterschappen) en instellingen 
uit de (semi-) publieke sector. 

[//]: # (Internationaal focus ook mogelijk, via Michiel naar OpenID)

### Doelgroep
De doelgroep van de standaard omvat organisaties die betrokken zijn bij het maken en verantwoorden van 
toegangsbeslissingen, zowel binnen de eigen organisatie als binnen samenwerkingsverbanden met andere partijen. Dit kan 
onder meer de Nederlandse overheid en andere relevante instanties omvatten.

## Terminologie 

De volgende lijst beschrijft terminologie in de betekenis zoals deze wordt gebruikt in dit document.

**Actie**

Een Dataverwerking bestaat uit één of meerdere kleinere discrete stappen. Een Actie is één discrete stap binnen een Dataverwerking.

**Applicatie**

Iedere softwaretoepassing waarmee Dataverwerkingen worden uitgevoerd.

**Betrokkene**

Als gegevens van rechtssubjecten door de overheid worden verwerkt, worden subjecten van wie de organisatie gegevens verwerkt de 'Betrokkene' genoemd. Betrokkenen in het kader van deze standaard kunnen zowel natuurlijke personen als rechtspersonen (bedrijven) zijn die met de verwerkte gegevens geïdentificeerd kunnen worden. Als identificeerbaar wordt beschouwd als een (rechts)persoon die direct of indirect kan worden geïdentificeerd, met name aan de hand van een identificerend gegeven zoals een (bedrijfs)naam, een identificatienummer, locatiedata of van een of meer elementen die kenmerkend zijn voor de fysieke, fysiologische, genetische, psychische, economische, culturele of sociale identiteit van die (rechts)persoon.

**Dataverwerking**

Iedere bewerking (of ieder geheel van bewerkingen) met betrekking tot gegevens, al dan niet uitgevoerd via geautomatiseerde procedures, zoals het verzamelen, vastleggen, ordenen, structureren, opslaan, bijwerken of wijzigen, opvragen, raadplegen, gebruiken, verstrekken door middel van doorzending, verspreiden of op andere wijze ter beschikking stellen, aligneren of combineren, afschermen, wissen of vernietigen van gegevens wordt opgevat als een Dataverwerking. Iedere Dataverwerking bestaat uit één of meerdere Acties.

**Inzage**

De Betrokkene heeft het recht om van de Verantwoordelijke uitsluitsel te verkrijgen over het al dan niet verwerken van hem betreffende gegevens en, wanneer dat het geval is, om inzage te verkrijgen van die gegevens. Voor natuurlijke personen sluit dit aan bij hun recht op inzage zoals bedoeld in de AVG, voor rechtspersonen([AVG], art. 15, lid 1). Voor rechtspersonen sluit dit aan bij het recht op transparantie over besluitvorming waar zij bij betrokken zijn. Met Inzage doelen we op de handeling waarmee uitvoering wordt gegeven aan dat recht.

**Logboek**

Softwaretoepassing waarmee het log van Dataverwerkingen wordt bijgehouden.

**Logregel**

Resultaat van een enkele gebeurtenis in de logging.

**Register**

Register waarin statische data over Verwerkingsactiviteiten worden geregistreerd en ter beschikking gesteld.

**Trace**

Concept waarmee bij elkaar behorende Dataverwerkingen binnen de grenzen van een systeem worden gegroepeerd.

**Verwerkingsverantwoordelijke**

Een natuurlijke persoon of rechtspersoon, een overheidsinstantie, een dienst of een ander orgaan die/dat, alleen of samen met anderen, het doel van en de middelen voor de verwerking van data vaststelt. Deze definitie is gebaseerd op ([AVG] art. 4, lid 7.), maar laat de verantwoordelijkheid betrekking hebben op de verwerking van álle data, niet alleen persoonsdata.

<p class="note" title=""> 
In de standaard wordt de Verwerkingsverantwoordelijke aangeduid als de Verantwoordelijke voor de leesbaarheid.

**Verwerker**

Een natuurlijke persoon of rechtspersoon, een overheidsinstantie, een dienst of een ander orgaan die/dat ten behoeve van de Verwerkingsverantwoordelijke persoonsdata verwerkt. Deze definitie is gebaseerd op ([AVG] art. 4, lid 8.), maar laat de verantwoordelijkheid betrekking hebben op de verwerking van álle data, niet alleen persoonsdata.

**Verwerkingsactiviteit**

Activiteiten die een organisatie onderkent heeft als activiteiten waarbinnen Dataverwerkingen plaatsvinden.