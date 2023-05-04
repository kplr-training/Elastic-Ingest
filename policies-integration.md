## Création d'une Agent Policy

Dans Elasticsearch, une `agent policy` fait référence à une fonctionnalité spécifique de l'outil d'ingestion de données appelé `Elastic Agent`. Elastic Agent est un composant qui permet de collecter et d'envoyer des données vers Elasticsearch de manière centralisée.

Une agent policy définit un ensemble de configurations et de règles pour la collecte de données à partir de sources spécifiques, telles que des fichiers journaux, des métriques système, des événements de base de données, etc. Elle fournit un moyen de déployer ces configurations sur plusieurs agents Elastic installés sur différents hôtes ou machines.

Pour créer une Agent Policy, redirigez vous vers l'interface `Fleet` dans le menu `Management` et cliquez `Agent Policies`: 

![image](https://user-images.githubusercontent.com/123748177/236157607-83af01a3-7715-4f93-8ad6-730051ab9d79.png)

Cliquez `Create Agent policy`, et remplissez les champs nécessaires: 

![image](https://user-images.githubusercontent.com/123748177/236158502-1e4c667c-82f9-47a1-af87-f2b53eeeff2e.png)

**Dans la partie `Advanced Options`, vous pouvez constater que l'Output des intégrations liées à cette policy ne peuvent avoir comme Output que celui de Elasticsearch par défaut.**

Pour le moment, vous choisissez l'Output par défaut et vous cliquez `Create Agent policy` 

![image (10)](https://user-images.githubusercontent.com/123748177/236152704-645981e8-e949-42e0-b16c-0160e5d13c69.png)

Pour pouvoir utiliser l'Output de type Logstash, vous devez avoir une `licence`. Donc pour ce faire, redirigez vous vers `Dev Tools` et tapez la commande suivante: 

```
POST /_license/start_trial?acknowledge=true&pretty
```
Puis, vous pouvez vérifier que vous avez obtenu la licence avec la commande suivante: 

```
GET /_license/trial_status?pretty
```

![image (14)](https://user-images.githubusercontent.com/123748177/236161726-6e45cbdc-5c2b-4eb6-bcb2-f25b5026461b.png)

Maintenant, si vous modifiez la policy que vous venez de créer, vous constatez que vous pouvez choisir `Logstash Output` comme sortie pour les intégrations liées à cette policy.

![image (9)](https://user-images.githubusercontent.com/123748177/236152709-2c509ee2-7da1-4395-bfad-53fea46fc0c5.png)

## Ajout d'un Custom Windows Event Logs Integration

Pour ajouter une intégration de type `Custom Windows Event Logs`, redirigez vous vers l'interface `Integrations` dans le menu `Management`

![image](https://user-images.githubusercontent.com/123748177/234854155-b648f4d5-c6a3-4373-ba68-5dc42cb26d0c.png)

Puis, vous cherchez `Custom Windows Event Logs`: 

![image](https://user-images.githubusercontent.com/123748177/234855005-9f8db042-064d-4388-af04-6bfb1ddcf304.png)

Maintenant, cliquez `Add Custom Windows Event Logs`

![image](https://user-images.githubusercontent.com/123748177/234855195-03e9c3c3-f325-4bb9-b125-8d43e95756a4.png)

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


![image (12)](https://user-images.githubusercontent.com/123748177/236152697-3af123e8-57c8-44b9-844d-35fe4768ba3e.png)
![image (11)](https://user-images.githubusercontent.com/123748177/236152700-3ab0f0e1-c722-4dfa-aad8-f91eed6c31bd.png)
![image (13)](https://user-images.githubusercontent.com/123748177/236152713-76e39b88-44bd-4b68-b6ab-16c2acc7240f.png)



