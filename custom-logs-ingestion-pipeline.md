## Custom Logs Ingestion Pipeline

Le package personnalisé pour les journaux d'événements Windows vous permet d'ingérer des événements à partir de n'importe quel canal de journal d'événements Windows. 

Vous pouvez obtenir une liste des canaux de journal d'événements disponibles en exécutant la commande suivante dans PowerShell :

```
Get-WinEvent -ListLog * | Format-List -Property LogName
```
Vous aurez toute la liste des canaux de journal d'événements disponibles affichée de la forme suivante: 

![image](https://user-images.githubusercontent.com/123748177/234847355-b5f3aaa7-2ba5-4401-bdef-79e740381b2b.png)


- **Ce qui nous intèresse c'est le canal `Microsoft-Windows-WMI-Activity/Operational` qu'on va ingérer par la suite** 

## Création d'un Ingest Pipeline

Vous pouvez créer votre Ingest Pipeline de deux façons: 

- *Méthode 1 :* l'aide de l'interface `Stack Management`.
- *Méthode 2 :* En utilisant les API d'ingestion.

**Méthode 1**
Dans Kibana, ouvrez le menu principal et cliquez sur `Stack Management`, puis sur `Ingest Pipelines`. 

À partir de la vue de liste, vous pouvez :

- Afficher une liste de vos pipelines et accéder aux détails de chacun.
- Modifier ou cloner des pipelines existants.
- Supprimer des pipelines.

Pour créer un pipeline, cliquez sur `Create pipeline` puis sur `New pipeline`.

[Puis, suivez ce tutoriel...](https://www.elastic.co/guide/en/elasticsearch/reference/current/common-log-format-example.html)

[Processor Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/processors.html)

**Méthode 2**

Vous pouvez également utiliser les API d'ingestion pour créer et gérer des pipelines. 

Pour ce faire, utilisez `Dev Tools` de Kibana.

- Voici un exemple de la création d'un ingest pipeline: 

```
PUT _ingest/pipeline/my-pipeline
{
  "description": "My optional pipeline description",
  "processors": [
    {
      "set": {
        "description": "My optional processor description",
        "field": "my-long-field",
        "value": 10
      }
    },
    {
      "set": {
        "description": "Set 'my-boolean-field' to true",
        "field": "my-boolean-field",
        "value": true
      }
    },
    {
      "lowercase": {
        "field": "my-keyword-field"
      }
    }
  ]
}
```

Vous pouvez vérifier que votre pipeline est bien créé à partir de l'interface `Ingest Pipelines` dans `Stack Management`.

![image](https://user-images.githubusercontent.com/123748177/234852105-6477952a-ad99-480f-9a2a-ea04f9912475.png)

Vous pouvez également visualiser les processors: 

![image](https://user-images.githubusercontent.com/123748177/234852262-c483bdca-8f2f-4f62-b3af-6416dfed0bc3.png)

## Ajout d'un Custom Windows Event Logs Integration

Pour ajouter une intégration de type `Custom Windows Event Logs`, redirigez vous vers l'interface `Integrations` dans le menu `Management`.

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




