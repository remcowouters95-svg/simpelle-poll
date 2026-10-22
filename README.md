# Simpelle Poll App voor Simpelle

Een poll op één pagina voor ideeën voor de vriendendag. Iedereen met de link kan een idee toevoegen (titel, omschrijving, link) en per idee nee, neutraal of ja kiezen (telt als -1, 0 of +1). Je vult één keer je naam in; die staat bij je stem en wordt in je browser onthouden.

## Opslag

De ideeën en stemmen staan in een gratis Supabase project (Postgres, regio Frankfurt):

- `ideas`: id, title, description, link, created_at
- `votes`: idea_id, voter, value (-1/0/1); één rij per persoon per idee
- `settings`: alleen de beheercode, niet leesbaar vanuit de browser

De pagina praat via de publishable key direct met Supabase. Row Level Security staat aan: anonieme bezoekers mogen lezen, ideeën toevoegen en stemmen toevoegen, wijzigen of intrekken. Niemand kan ideeën verwijderen, behalve via de functie `delete_idea(id, code)`, die de code tegen `settings` controleert.

Wijzigingen komen bij alle open pagina's live binnen via Supabase Realtime.

## Beheer

Onderaan de pagina staat Beheer. Vul daar de beheercode in (eenmalig per browser), dan verschijnt bij elk idee een knop Schrappen met bevestiging. De code staat in de tabel `settings` in Supabase en is te wijzigen met:

```sql
update public.settings set value = 'nieuwe-code' where key = 'admin_code';
```

## Hosten

`index.html` is de hele app. Zet de repo op GitHub Pages (Settings > Pages > Deploy from branch `main`, map `/`) en de poll staat online.

## Bekende grenzen

- De naam is vrije tekst, dus iemand kan onder een andere naam stemmen. Voor een vriendengroep is dat prima.
- Tegelijk stemmen is geen probleem; elke stem is een eigen rij.
