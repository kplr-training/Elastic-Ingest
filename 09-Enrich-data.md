## Example: Enrich your data based on exact values

Les politiques d'enrichissement de correspondance associent les données d'enrichissement aux documents entrants en fonction d'une valeur exacte, telle qu'une adresse e-mail ou un identifiant, à l'aide d'une requête de terme.

**Objectif**

L'exemple suivant crée une politique d'enrichissement de correspondance qui ajoute le nom d'utilisateur et les informations de contact aux documents entrants en fonction d'une adresse e-mail.

Ensuite, il ajoute la politique d'enrichissement de correspondance à un processeur dans un pipeline d'ingestion.

Vous allez utilisez l'API de création d'index ou l'API d'indexation pour créer un index source.

La requête API d'indexation suivante crée un index source et indexe un nouveau document dans cet index: 

```
PUT /users/_doc/1?refresh=wait_for
{
  "email": "mardy.brown@asciidocsmith.com",
  "first_name": "Mardy",
  "last_name": "Brown",
  "city": "New Orleans",
  "county": "Orleans",
  "state": "LA",
  "zip": 70116,
  "web": "mardy.asciidocsmith.com"
}

```

Cette commande va insérer ce document dans un index appelé "users", mais comme l'index "users" n'est pas encore configuré dans Elasticsearch, il le créera automatiquement dès que vous exécutez la commande. 

Elasticsearch effectuera le mapping automatique pour chaque champ de ce document. 

- **Vous pouvez vérifier que l'index `users` est bien créé dans la partie `Index Management`**

Maintenant, vous utilisez l'API de création de politique d'enrichissement `create enrich policy API` pour créer une politique d'enrichissement avec le type de politique "match" `match policy type`. 

Cette politique doit inclure :

- Un ou plusieurs index source (source indices)
- Un champ de correspondance (match_field), c'est-à-dire le champ des index source utilisé pour faire correspondre les documents entrants
- Des champs d'enrichissement (enrich fields) provenant des index source que vous souhaitez ajouter aux documents entrants.

```
PUT /_enrich/policy/users-policy
{
  "match": {
    "indices": "users",
    "match_field": "email",
    "enrich_fields": ["first_name", "last_name", "city", "zip", "state"]
  }
}
```

Vous devez utiliser l'API d'exécution de politique d'enrichissement pour créer un index enrichi pour la politique. 

```
POST /_enrich/policy/users-policy/_execute
```

*NB: cette commande doit etre réexécuter à chaque fois que vous ajoutez des nouveaux documents*

**Création d'un Ingest Pipeline**

Ce que fait le pipeline d'ingestion, c'est qu'il indique à Elasticsearch quelle politique utiliser ou quelle politique d'enrichissement utiliser pour enrichir les documents entrants avec.

```
PUT /_ingest/pipeline/user_lookup
{
  "processors" : [
    {
      "enrich" : {
        "description": "Add 'user' data based on 'email'",
        "policy_name": "users-policy",
        "field" : "email",
        "target_field": "user",
        "max_matches": "1"
      }
    }
  ]
}
```
Cette commande permet de configurer un pipeline d'ingestion appelé "user lookup" et vous allez utiliser la politique `users-policy` que vous avez créé. 

Ce pipeline d'ingestion permet de prendre le champ de l'e-mail et de le relier à la politique `users-policy`. 

La politique permet de faire la correspondance et faire fonctionner l'enrichissement.

Une fois la correspondance effectuée, vous allez prendre les données enrichies, qui comprennent le prénom, le nom, le code postal, et l'État puis les stocker dans un champ imbriqué appelé `user`. 

Il est possible qu'il y ait plusieurs utilisateurs renvoyés si plusieurs utilisateurs ont la même adresse e-mail. 

Dans ce cas, on fixe le `max_matches` en 1 vu que vous voulez renvoyer uniquement un seul document.

**Vous pouvez vérifier que votre ingest pipeline est bien créé en cherchant dans `Stack Management` > `Ingest Pipelines`**


![image](https://user-images.githubusercontent.com/123748177/234878411-02555fa6-36c6-492d-81b3-d72c94542d45.png)

Vous allez insérer maintenant un nouveau document avec uniquement un champ "email". 

Vous allez spécifier explicitement l'utilisation de ce pipeline d'ingestion que vous venez de configurer pour obtenir les données enrichies. 

```
PUT /my-index-000001/_doc/my_id?pipeline=user_lookup
{
  "email": "mardy.brown@asciidocsmith.com"
}
```

Pour vérifier si le processeur d'enrichissement a effectué une correspondance et ajouté les données de champ appropriées, utilisez l'API "get" pour afficher le document indexé.

```
GET /my-index-000001/_doc/my_id
```
Vous aurez le résultat suivant :

```
{
  "found": true,
  "_index": "my-index-000001",
  "_id": "my_id",
  "_version": 1,
  "_seq_no": 55,
  "_primary_term": 1,
  "_source": {
    "user": {
      "email": "mardy.brown@asciidocsmith.com",
      "first_name": "Mardy",
      "last_name": "Brown",
      "zip": 70116,
      "city": "New Orleans",
      "state": "LA"
    },
    "email": "mardy.brown@asciidocsmith.com"
  }
}
```

