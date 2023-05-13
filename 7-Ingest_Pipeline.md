## Les pipelines et les processeurs

Les pipelines sont une fonctionnalité clé d'Elastic Stack qui permet de transformer et d'enrichir les données avant leur indexation dans Elasticsearch.

Les pipelines sont souvent utilisés pour normaliser les données provenant de sources différentes et pour s'assurer que les données sont prêtes à être analysées. Les pipelines peuvent également être utilisés pour filtrer les données ou appliquer des règles métier spécifiques avant leur indexation.

Elastic Stack fournit plusieurs types de processeurs, tels que le Grok Processor, le Dissect Processor, le CSV Processor, le Rename Processor, le Lowercase Processor, etc. Les processeurs peuvent être utilisés dans différentes combinaisons pour créer des pipelines personnalisés pour répondre aux besoins spécifiques de chaque application.

Les pipelines peuvent être configurés au niveau de l'index ou au niveau du cluster Elasticsearch. Les pipelines configurés au niveau de l'index s'appliquent uniquement à cet index, tandis que les pipelines configurés au niveau du cluster s'appliquent à tous les index.

## Ingest Pipeline

Les pipelines d'ingestion vous permettent d'effectuer des transformations courantes sur vos données avant leur indexation. Par exemple, vous pouvez utiliser des pipelines pour supprimer des champs, extraire des valeurs à partir de texte et enrichir vos données.

Un pipeline se compose d'une série de processeurs. Chaque processeur s'exécute séquentiellement et apporte des modifications spécifiques aux documents entrants. Une fois que les processeurs ont terminé leur exécution, Elasticsearch ajoute les documents transformés à votre flux de données ou à votre index.

**Création d'une Ingest Pipeline**

Pour créer votre Ingest Pipeline, redirigez vous vers le menu `Stack management` et cliquez sur `Ingest Pipelines`:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/a5e64dc3-6701-4f03-bc9d-8a493975cb60)

- Vous aurez la page suivante, cliquez sur `Create pipeline`:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/891cc2c8-e0e9-4822-90c5-8bb96e74ee2f)

- Saissisez un nom pour votre pipeline!
- Ajoutez votre premier processeur en cliquant sur `Add a processor`.

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/0bf7e381-d4ee-4253-a6ea-f9d336816981)

Vous allez utiliser comme processeur, le processeur Grok qui est utilisé pour extraire des informations structurées à partir des données non structurées ou semi-structurées. Il s'appuie sur des expressions régulières prédéfinies ou personnalisées, appelées patterns, pour analyser et découper les données en champs plus significatifs.

Votre processeur Grok sera utilisé pour extraire: l'adresse IP, le nom de la ville, et le nom de pays à partir des logs qui se structure comme cet exemple: `154.326.23.24#RABAT#MOROCCO`.

- Ajoutez la configuration suivante:


`Processor` : `Grok`

`Field` : `message`

`Patterns` : `%{IPORHOST:a_source_ip}#%{USER:a_city}#%{USER:a_country}`


![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/d26b94f3-b921-4388-be4d-ee86a990888d)

- Lorsque vous créez votre processeur avec succès, il vous reste seulement de le tester. Pour ce faire, Cliquez `Add documents`.

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/2f510698-7ed1-45cd-a770-e3638bb5783f)

- Puis, ajoutez le document suivant pour vérifier si la pipeline fonctionne correctement:

```
[
  {
    "_source": {
      "@timestamp": "2023-05-10T09:16:31.908Z",
      "message": "MEHDI#CASABLANCA#MOROCCO"
    }
  }
]
```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/411ccb10-d684-46ce-a226-6e5db290a8fa)


- Cliquez sur `Run the pipeline`, et vérifiez que vous avez bien les nouveaux champs: `a_source_ip`, `a_city`, `a_country` :

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/cf30d470-bf87-4c1e-91cc-a86d23fa0aec)

- Si votre pipeline fonctionne correctement, sauvegardez la pipeline.

- Pour utiliser la pipeline et l'appliquer sur les données reçus depuis l'agent Elastic, redirigez vers l'intégration que vous avez créé préalablement puis ajoutez le nom de la pipeline dans le champ `Custom configurations` dans `Advanced options`:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/807c1bc8-0843-4e5a-abaa-812a7855c5bf)

- Maintenant, ajoutez une ligne de log dans votre fichier pour tester la pipeline: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/da121af9-9895-434b-9596-f213abd028ce)

- Vérifiez le document dans votre data view dans `Discover`: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/d1d63e76-7649-4b43-bdb9-65c18c3b634e)

**Félicitations! vous avez créé et exécuté votre première pipeline**




