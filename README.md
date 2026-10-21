# Simpelle Poll App voor Simpelle

Een poll op één pagina voor ideeën voor de vriendendag. Iedereen met de gedeelde link kan een idee toevoegen (titel, omschrijving, link) en per idee één stem geven of weer intrekken.

## Hoe het werkt

- `index.html` is de hele app: opmaak, markup en script in één bestand.
- De stand (ideeën en stemmen) staat als JSON in het bestand zelf, in `<script id="state" type="application/json">`.
- De pagina draait als Claude artifact met de `artifact` capability: bij elke stem of toevoeging herschrijft de pagina zichzelf met de nieuwe stand en publiceert die, zodat iedereen dezelfde versie ziet.
- Welke ideeën jij al hebt gestemd staat in `localStorage` van je eigen browser, zodat je per idee maar één keer kunt stemmen.

## Buiten Claude gebruiken

Zonder de artifact runtime werkt de pagina als alleen lezen. Wil je hem ergens anders hosten, vervang dan `artifact.publish(...)` door een eigen opslag (bijvoorbeeld een klein JSON endpoint).
