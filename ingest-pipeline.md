Les pipelines d'ingestion vous permettent d'effectuer des transformations courantes sur vos données avant leur indexation. Par exemple, vous pouvez utiliser des pipelines pour supprimer des champs, extraire des valeurs à partir de texte et enrichir vos données.

Un pipeline se compose d'une série de tâches configurables appelées processeurs. Chaque processeur s'exécute séquentiellement et apporte des modifications spécifiques aux documents entrants. Une fois que les processeurs ont terminé leur exécution, Elasticsearch ajoute les documents transformés à votre flux de données ou à votre index.

## Prérequis :
Les noeuds avec le rôle de `noeud d'ingestion` gèrent le traitement des pipelines. Pour utiliser les pipelines d'ingestion, votre cluster doit avoir au moins un noeud avec le rôle d'ingestion. Pour des charges d'ingestion importantes, nous vous recommandons de créer des noeuds d'ingestion dédiés.

Vous pouvez définir le rôle d'ingestion pour un nœud en ajoutant la configuration suivante dans le fichier elasticsearch.yml :
```
node.ingest: true

```
Si les fonctionnalités de sécurité d'Elasticsearch sont activées, vous devez disposer du privilège de cluster `manage_pipeline` pour gérer les pipelines d'ingestion. Par exemple, pour accorder ce privilège à un utilisateur avec l'API REST :

```
PUT /_security/user/<username>
{
  "cluster": ["manage_pipeline"]
}

```

Pour utiliser la fonctionnalité des pipelines d'ingestion de Kibana, vous avez également besoin des privilèges de cluster:monitor/nodes/info.
```
PUT /_security/user/<username>/_privileges
{
  "cluster": ["cluster:monitor/nodes/info"]
}


```

Les pipelines comprenant le processeur `enrich` nécessitent une configuration supplémentaire. 