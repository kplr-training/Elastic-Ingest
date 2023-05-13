## Integration Processors

L'avantage d'utiliser des processeurs d'intégration est qu'ils permettent de traiter les données en amont, directement lors de l'indexation dans Elasticsearch, évitant ainsi les étapes de post-traitement coûteuses. Cela garantit une ingestion plus rapide et une utilisation efficace des ressources.

**- Ajout d'un simple processor**

Dans cette partie, vous allez définir un simple processeur dans l'intégration que vous venez de créer.

- Redirigez vous vers l'intégration que vous venez de créer et puis la modifiez.

- Vous trouvez la partie `Advanced Options`, le champ `Processors` dans lequel vous allez dénifir vos processeurs. Ajoutez le code suivant: 

```
- add_fields:
    target: a.integration.processor
    fields:
      name: asana
      id: '574734885120952459'

```

**En utilisant ce processeur d'intégration, chaque document qui passe par le pipeline d'ingestion aura les champs "name" et "id" ajoutés avec les valeurs spécifiées.**

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/a6759e92-a32b-4590-a041-f7ac311c3868)

- Enregistrez les modifications de l'intégration, puis ajoutez une nouvelle ligne de log pour vérifier que les processeurs de l'intégration fonctionnent correctement: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/db5c0715-d6bd-4f45-b7c1-945ba3356084)

- Vérifiez dans `Discover`

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/ec811480-e261-4c1c-8ae0-6bf710f6fd7e)

**Les nouveaux champs sont bien ajoutés**

- Bravo. A vous de jouter maintenant. Allez faire un tour sur la doc et voir les autre processeurs possibles. 
- Choisissez-en quelques-un et implémentez-en certains. 
- Vous pouvez alimenter les logs de la façon dont vous souhaitez, en concordance avec les processeurs choisis. 

### Documentation des processeurs : 

- Nota : Les processeurs d'intégrations intégrés sont ceux de FileBeat.

https://www.elastic.co/guide/en/beats/filebeat/current/filtering-and-enhancing-data.html
