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

