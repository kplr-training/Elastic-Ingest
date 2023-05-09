## Les pipelines et les processeurs

Les pipelines sont une fonctionnalité clé d'Elastic Stack qui permet de transformer et d'enrichir les données avant leur indexation dans Elasticsearch.

Les pipelines sont souvent utilisés pour normaliser les données provenant de sources différentes et pour s'assurer que les données sont prêtes à être analysées. Les pipelines peuvent également être utilisés pour filtrer les données ou appliquer des règles métier spécifiques avant leur indexation.

Elastic Stack fournit plusieurs types de processeurs, tels que le Grok Processor, le Dissect Processor, le CSV Processor, le Rename Processor, le Lowercase Processor, etc. Les processeurs peuvent être utilisés dans différentes combinaisons pour créer des pipelines personnalisés pour répondre aux besoins spécifiques de chaque application.

Les pipelines peuvent être configurés au niveau de l'index ou au niveau du cluster Elasticsearch. Les pipelines configurés au niveau de l'index s'appliquent uniquement à cet index, tandis que les pipelines configurés au niveau du cluster s'appliquent à tous les index.


- Créez un fichier, dans la machine où vous avez installé l'Elastic Agent, dans lequel vous allez stocker vos logs de test :
 ```
 vi test.log
 ```
