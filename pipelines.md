## Les pipelines et les processeurs

Les pipelines sont une fonctionnalité clé d'Elastic Stack qui permet de transformer et d'enrichir les données avant leur indexation dans Elasticsearch.

Les pipelines sont souvent utilisés pour normaliser les données provenant de sources différentes et pour s'assurer que les données sont prêtes à être analysées. Les pipelines peuvent également être utilisés pour filtrer les données ou appliquer des règles métier spécifiques avant leur indexation.

Elastic Stack fournit plusieurs types de processeurs, tels que le Grok Processor, le Dissect Processor, le CSV Processor, le Rename Processor, le Lowercase Processor, etc. Les processeurs peuvent être utilisés dans différentes combinaisons pour créer des pipelines personnalisés pour répondre aux besoins spécifiques de chaque application.

Les pipelines peuvent être configurés au niveau de l'index ou au niveau du cluster Elasticsearch. Les pipelines configurés au niveau de l'index s'appliquent uniquement à cet index, tandis que les pipelines configurés au niveau du cluster s'appliquent à tous les index.

## Création d'une nouvelle intégration

Dans cette partie, vous allez créé une intégration de type `Custom logs`:

Pour ajouter cette intégration, redirigez vous vers l'interface `Integrations` dans le menu `Management`.

![image](https://user-images.githubusercontent.com/123748177/234854155-b648f4d5-c6a3-4373-ba68-5dc42cb26d0c.png)

Puis, vous cherchez `Custom Logs`: 

![custom logs](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/4201dd6f-1a1a-4b28-925b-347181284106)

Maintenant, cliquez `Add Custom Logs`

![add custom log](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/f64f4961-742c-49fb-994a-4310c002c1de)

- Vous donnez d'abord un nom de votre instance d'intégration :
![image](https://user-images.githubusercontent.com/123748177/234855430-1f2a7242-d3d2-4331-a872-65582cf38f49.png)

- Le nom du canal doit correspondre au canal à partir duquel vous avez l'intention d'extraire des informations.
- Dans votre cas, on propose que vous choissisez comme nom de canal `Microsoft-Windows-WMI-Activity/Operational` et comme nom de dataset `test-app-1`

![image](https://user-images.githubusercontent.com/123748177/234856197-fc8808bf-711b-4c77-89bc-e277365b0fcd.png)

- Pour utilisez la pipeline que vous avez créé, vous devez la définir dans la partie `Advanced options` > `Custom configurations`.

![image](https://user-images.githubusercontent.com/123748177/234856717-a3d395fc-d6c0-4cc9-9fab-aea94bd678a8.png)

- Finalement, vous précisez la policy auquel l'intégration va etre ajoutée.

![image](https://user-images.githubusercontent.com/123748177/234857165-e620b570-55f2-41dc-9416-ee203a160ee7.png)

- Pour vérifiez que tout marche bien, redirigez vous vers `Analytics` > `Discover` 

![image](https://user-images.githubusercontent.com/123748177/234858330-ac6a7b8a-e8be-4608-b131-2ce80135741d.png)

- Puis, dans la partie de recherche tapez le code suivant en ajoutant le nom du dataset que vous avez précisez avant :
```
event.dataset: "YOUR-DATASET-NAME"
```
- Vous aurez un résultat comme le suivant: 

![image](https://user-images.githubusercontent.com/123748177/234858963-ef4b9ee9-af6c-4378-8bd7-c9d50d80ad46.png)



## Logstash Pipeline

Une pipeline Logstash se compose de trois étapes principales : l'étape d'entrée, qui récupère les données, l'étape de filtrage, où les données sont transformées et enrichies, et l'étape de sortie, qui détermine où les données traitées seront envoyées. 

Chaque étape utilise des plugins configurables pour effectuer des opérations spécifiques sur les données. La configuration de la pipeline est définie dans un fichier de configuration, et une fois la pipeline lancée, Logstash traite continuellement les données selon cette configuration. 

Les pipelines Logstash offrent une grande flexibilité pour répondre aux besoins spécifiques de collecte, de transformation et de livraison des données.


- Créez un fichier, dans la machine où vous avez installé l'Elastic Agent, dans lequel vous allez stocker vos logs de test :
 ```
 vi test.log
 ```
