## Sequelize-Auto (21/03/2020)
### Générer les modèles à partir de BDD existante

[Pour Sequelize-Auto c'est par ici](https://github.com/sequelize/sequelize-auto)
Cet outil permet de générer vos modèles Sequelize à partir de vos bases de données existantes.
Exemple d'installation pour utilisation avec PostgreSQL

```
npm install -g sequelize-auto pg pg-hstore
sequelize-auto -o "./models" -d MABASE -h localhost -u MONUSER -p 5432 -x MONPASSWORD -e postgres
```

et voilà vos modèles créés !

### Ajout de champs "virtuels"

Quand on veut traiter les resultats retournés par Sequelize pour ajouter des données par exemple avant de faire un ```res.json(result)``` il faut dans le modèle créer des champs de type VIRTUAL : 

``` 
  monNouveauChamps: {
    type: DataTypes.VIRTUAL
  } 
```
