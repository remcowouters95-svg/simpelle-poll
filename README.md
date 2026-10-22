# Simpelle Poll App voor Simpelle

Een poll op één pagina voor ideeën voor de vriendendag. Iedereen met de link kan een idee toevoegen (titel, omschrijving, link) en per idee nee, neutraal of ja kiezen (telt als -1, 0 of +1). Onder elk idee kun je een opmerking of een verbetersuggestie plaatsen; die staan in een uitklapbaar blokje met je naam erbij. Je vult één keer je naam in; die staat bij je stem en je reacties en wordt in je browser onthouden.

## Opslag

De ideeën en stemmen staan in een gratis Supabase project (Postgres, regio Frankfurt):

- `ideas`: id, title, description, link, created_at
- `votes`: idea_id, voter, value (-1/0/1); één rij per persoon per idee
- `comments`: id, idea_id, author, kind (`opmerking` of `suggestie`), body, created_at
- `settings`: alleen de beheercode, niet leesbaar vanuit de browser
- `change_requests`: verzoeken van de beheerder om de app aan te passen (author, body, status, answer, pr_url); alleen bereikbaar via de functies `request_change(code, author, body)`, `list_change_requests(code)` en `withdraw_change_request(code, id)`, die de beheercode controleren

De pagina praat via de publishable key direct met Supabase. Row Level Security staat aan: anonieme bezoekers mogen lezen, ideeën toevoegen, stemmen toevoegen, wijzigen of intrekken, en reacties plaatsen of verwijderen. Niemand kan ideeën verwijderen, behalve via de functie `delete_idea(id, code)`, die de code tegen `settings` controleert. Schrap je een idee, dan gaan de stemmen en reacties erbij mee weg.

Wijzigingen komen bij alle open pagina's live binnen via Supabase Realtime.

## Beheer

Onderaan de pagina staat Beheer. Vul daar de beheercode in (eenmalig per browser), dan verschijnt bij elk idee een knop Schrappen met bevestiging en bij elke reactie een knop Weg. Ook verschijnt het blok Aanpassing aanvragen.

### Aanpassing aanvragen

De beheerder beschrijft in dat blok wat er anders moet aan de app. Het verzoek komt in `change_requests` met status `nieuw`. Een scheduled task in Claude Code kijkt elk uur (overdag en 's avonds) of er nieuwe verzoeken zijn, bouwt de aanpassing op een eigen branch, opent een draft pull request en zet de status op `klaar` met een korte uitleg en de link naar de pull request. De eigenaar van de repo keurt de pull request goed, daarna staat de aanpassing op GitHub Pages. Een verzoek dat onduidelijk, onveilig of niet haalbaar is krijgt status `afgewezen` met uitleg. De beheerder ziet status en antwoord in hetzelfde blok en kan een verzoek intrekken zolang de status nog `nieuw` is (het verzoek blijft bewaard met `withdrawn = true`, maar wordt niet meer getoond of opgepakt). De code staat in de tabel `settings` in Supabase en is te wijzigen met:

```sql
update public.settings set value = 'nieuwe-code' where key = 'admin_code';
```

## Hosten

`index.html` is de hele app. Zet de repo op GitHub Pages (Settings > Pages > Deploy from branch `main`, map `/`) en de poll staat online.

## Bekende grenzen

- De naam is vrije tekst, dus iemand kan onder een andere naam stemmen. Voor een vriendengroep is dat prima.
- Tegelijk stemmen is geen probleem; elke stem is een eigen rij.
- Een reactie kun je zelf weghalen als je onder dezelfde naam bent ingelogd als waarmee je hem plaatste; de beheerder kan alles weghalen.
- Een half getypte reactie blijft staan als er intussen een live update binnenkomt.
