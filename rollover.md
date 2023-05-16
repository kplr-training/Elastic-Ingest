## Rollover

Dans Elasticsearch, le rollover est un mécanisme intégré qui permet la gestion automatique des index. Il permet de diviser les données d'un index en plusieurs segments plus petits et plus gérables, facilitant ainsi les opérations de recherche et d'analyse.

Le rollover est souvent utilisé pour gérer la croissance des données, maintenir des performances élevées et simplifier la gestion des index. Il est couramment utilisé dans les cas où les volumes de données sont importants et nécessitent une répartition efficace pour éviter les problèmes de performance.

### Liens importants
- https://www.elastic.co/guide/en/elasticsearch/reference/current/index-rollover.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-rollover-index.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/ilm-rollover.html
- 


### Comment fonctionne le rollover dans Elasticsearch?

Voici comment le rollover fonctionne dans Elasticsearch :

- Vous définissez une politique de rollover qui spécifie les conditions pour déclencher le rollover. Les conditions couramment utilisées incluent la taille de l'index, le nombre de documents ou la durée.

- Vous créez un alias qui sera utilisé pour effectuer les opérations d'indexation et de recherche. L'alias est associé à l'index initial et sera utilisé pour les opérations courantes.

- Vous créez un index initial qui sert de point de départ. Cet index est associé à l'alias. Les opérations d'indexation sont effectuées sur cet index.

- Lorsque les conditions définies dans la politique de rollover sont remplies (par exemple, lorsque la taille maximale de l'index est atteinte), Elasticsearch déclenche automatiquement le rollover.

- Lorsque le rollover est déclenché, Elasticsearch crée automatiquement un nouvel index. Les opérations d'indexation sont redirigées vers ce nouvel index.

- L'alias est automatiquement mis à jour pour pointer vers le nouvel index créé. Cela signifie que les opérations de recherche continueront de fonctionner sans interruption.

- Le processus de rollover peut être répété de manière cyclique à mesure que les nouvelles conditions de rollover sont satisfaites.

### Les conditions de déclenchemennt du rollover

Les conditions pour déclencher un rollover sont les suivantes :

- Taille de l'index : Vous pouvez spécifier une taille maximale pour un index, mesurée en octets, gigaoctets, mégaoctets, etc. Lorsque la taille de l'index actuel atteint cette limite, le rollover est déclenché et un nouvel index est créé pour continuer à stocker les données.

- Nombre de documents : Vous pouvez définir un nombre maximum de documents autorisés dans un index. Lorsque le nombre de documents atteint cette limite, Elasticsearch déclenche le rollover et crée un nouvel index pour continuer à ajouter des documents.

- Durée : Vous pouvez spécifier une durée prédéfinie après laquelle un rollover est déclenché, quel que soit le nombre de documents ou la taille de l'index. Par exemple, vous pouvez configurer un rollover mensuel, où un nouvel index est créé automatiquement chaque mois.

Une fois que les conditions du rollover sont satisfaites, Elasticsearch procède à la création d'un nouvel index, tout en conservant les anciens index pour la recherche et la récupération des données historiques si nécessaire.

### Rollover Retention

La durée de rétention du rollover (rollover retention) fait référence à la période pendant laquelle les données indexées lors du processus de rollover sont conservées dans le système avant d'être éventuellement supprimées.

Lorsqu'un rollover se produit, un nouvel index est créé pour stocker les nouvelles données, tandis que l'ancien index est conservé pendant une certaine durée de rétention. Cela permet de garantir que les données précédemment indexées restent accessibles pendant un certain temps, même après le rollover.

La durée de rétention du rollover peut être déterminée en fonction des besoins spécifiques de l'application ou du système. Elle peut varier de quelques jours à plusieurs semaines, voire plus longtemps, en fonction des exigences en matière d'archivage, d'analyse ou de conformité.

Il convient de noter que la durée de rétention du rollover est généralement configurable et peut être ajustée en fonction des politiques de rétention des données de l'entreprise ou des exigences réglementaires.

Dans Elasticsearch, vous pouvez configurer la durée de rétention des données en utilisant la fonctionnalité de rollover combinée avec des politiques d'indexation et de suppression. Voici quelques exemples de configuration :

### Exemple 1: Durée de rétention basée sur la date
Supposons que vous souhaitiez conserver les données pendant 30 jours avant de les supprimer. Voici comment vous pouvez configurer cela :

- Créez un indice initial avec un nom significatif, par exemple "logs-000001".
- Définissez une politique de rollover avec une clause "max_age" de 30 jours, qui spécifie la durée maximale de rétention des données avant le rollover. Par exemple :

```
PUT /logs-*/_rollover/my_policy
{
  "conditions": {
    "max_age": "30d"
  }
}
```
- Créez un alias pour l'indice initial et associez la politique de rollover à l'alias. Par exemple :

```
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "logs-000001",
        "alias": "logs",
        "is_write_index": true
      }
    },
    {
      "add": {
        "index": "logs-*",
        "alias": "logs",
        "is_write_index": false
      }
    }
  ]
}

```
Avec cette configuration, chaque fois que 30 jours se sont écoulés depuis la création de l'indice initial, un nouveau segment d'indice sera créé, et les anciens segments seront conservés pendant la durée de rétention spécifiée avant d'être éventuellement supprimés.

### Exemple 2: Durée de rétention basée sur le nombre de documents
Supposons que vous souhaitiez conserver jusqu'à 1 million de documents dans un indice avant de le rouler. Voici comment vous pouvez configurer cela :

- Créez un indice initial avec un nom significatif, par exemple "logs-000001".
- Définissez une politique de rollover avec une clause "max_docs" de 1 million, qui spécifie le nombre maximal de documents avant le rollover. Par exemple :
```
PUT /logs-*/_rollover/my_policy
{
  "conditions": {
    "max_docs": 1000000
  }
}
```

- Créez un alias pour l'indice initial et associez la politique de rollover à l'alias, de la même manière que dans l'exemple précédent.

Avec cette configuration, chaque fois que l'indice initial atteint 1 million de documents, un nouveau segment d'indice sera créé, et les anciens segments seront conservés avant d'être éventuellement supprimés.
