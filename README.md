# Simpelle Poll App voor Simpelle

Een poll op één pagina voor ideeën voor de vriendendag. Iedereen met de gedeelde link kan een idee toevoegen (titel, omschrijving, link) en per idee nee, neutraal of ja kiezen (telt als -1, 0 of +1). De naam van de stemmer komt er automatisch bij; alleen de eigenaar kan ideeën schrappen. Onderaan staat een knop Geef suggesties: de pagina vraagt Claude (via de `sample` capability) om drie nieuwe ideeën in de lijn van wat er al staat, elk met een knop om het meteen op de lijst te zetten.

## Hoe het werkt

- `index.html` is de hele app: opmaak, markup en script in één bestand.
- De stand (ideeën en stemmen) staat als JSON in het bestand zelf, in `<script id="state" type="application/json">`.
- De pagina draait als Claude artifact met de `artifact` capability: bij elke stem of toevoeging herschrijft de pagina zichzelf met de nieuwe stand en publiceert die, zodat iedereen dezelfde versie ziet.
- Stemmen staan per persoon in de pagina, gekoppeld aan het claude.ai account (of aan een zelf ingevulde naam voor kijkers zonder account; die naam onthoudt de browser in `localStorage`). Per persoon telt per idee één stem, die je altijd kunt wijzigen of intrekken.
- Stemmen twee mensen tegelijk, dan wint de eerste; de ander ziet een melding en kan opnieuw stemmen. Elke stem is een nieuwe versie, dus via de versiegeschiedenis kun je terug naar een eerdere stand.

## Buiten Claude gebruiken

Zonder de artifact runtime werkt de pagina als alleen lezen. Wil je hem ergens anders hosten, vervang dan `artifact.publish(...)` door een eigen opslag (bijvoorbeeld een klein JSON endpoint).
