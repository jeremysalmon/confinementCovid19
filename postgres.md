### Ajouter un champs à un JSONB (21/03/2020)
Comment ajouter une propriété à un champs JSONB quand il n'existe pas

```
update smtreclamation set customdata = jsonb_set(customdata, '{externalId}', '"gagOU"', true) where customdata->>'externalId' is null;
```

On ajoute la propriété customdata.externalId avec la valeur gagOU si elle n'existe pas.
Sinon rien ne change


### Copie une base vers une nouvelle base

```CREATE DATABASE nouvellebase TEMPLATE basedereference;```


