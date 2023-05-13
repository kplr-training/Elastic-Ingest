## Exemple : Analyser des journaux au format Common Log (CLF)

Dans ce tutoriel, vous utiliserez un pipeline d'ingestion pour analyser des journaux serveur au format Common Log avant l'indexation. 

Le format Common Log est un format standard utilisé pour enregistrer les journaux des serveurs web. Ce format est couramment utilisé par les serveurs web Apache et Nginx.

Les journaux au format Common Log sont des fichiers texte structurés qui enregistrent des informations telles que l'adresse IP du client, la date et l'heure de la requête, la méthode HTTP utilisée, l'URL demandée, le code de réponse du serveur, la taille de la réponse, etc. 

Ce format facilite l'analyse et la compréhension des activités des serveurs web.

Les journaux que vous souhaitez analyser ressemblent à ceci :

```
212.87.37.154 - - [05/May/2099:16:21:15 +0000] "GET /favicon.ico HTTP/1.1" 200 3638 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36"

```

Ces journaux contiennent un horodatage, une adresse IP et un utilisateur agent. 

**Vous allez attribuer à ces trois éléments leur propre champ dans Elasticsearch pour des recherches et des visualisations plus rapides. Vous allez également savoir d'où provient la requête.**

### Mise en place du pipeline: 

1- Dans Kibana, ouvrez le menu principal et cliquez sur "Stack Management" > "Ingest Pipelines".

