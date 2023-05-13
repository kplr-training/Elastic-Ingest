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

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/fb5caf44-0609-4af0-9451-92706f6691b2)

2- Cliquez sur "Create pipeline" > "New pipeline".

![Capture d’écran 2023-05-05 110826](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/ff3891f7-17bd-4b85-a105-ad4a69c05b1b)

3- Nommez votre pipeline "my-pipeline".

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/8842accf-cd2c-45ab-918a-b92ad181410b)

4- Ajoutez un processeur de type grok pour analyser le message du journal. Cliquez sur "Add processor" et sélectionnez le type de processeur Grok depuis la liste déroulante.

![processor](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/52c06220-a663-48c9-b904-c72eefebbfaa)

5- Définissez `Field` sur "message" et `Pattern` sur le motif grok suivant :

```
%{IPORHOST:source.ip} %{USER:user.id} %{USER:user.name} \[%{HTTPDATE:@timestamp}\] "%{WORD:http.request.method} %{DATA:url.original} HTTP/%{NUMBER:http.version}" %{NUMBER:http.response.status_code:int} (?:-|%{NUMBER:http.response.body.bytes:int}) %{QS:http.request.referrer} %{QS:user_agent}

```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/ab9e85c1-e29b-42da-8bca-58800dc11902)

6- Cliquez sur "Add" pour enregistrer le processeur.

7- Ajoutez autres processeurs pour les champs de l'horodatage, de l'adresse IP et de l'agent utilisateur. Configurez les processeurs comme suit :

**Horodatage**
```
Processor type: Date

Field: @timestamp

Formats: dd/MMM/yyyy:HH:mm:ss Z
```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/eac46012-579e-491e-a2f9-7f3fc4581103)

**Adresse IP**
```
Processor type: GeoIP

Field: source.ip

Target field: source.geo
```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/2578c681-dbef-4d61-a5bd-f551fae20e1f)

**Agent user**

```
Processor type: User agent

Field: user_agent
```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/871a931b-602a-4726-9c15-11947fe2ac06)

Votre formulaire devrait ressembler à ceci :

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/59136eae-245e-4930-87d4-0a89e7ff9f60)

**Les quatre processeurs s'exécuteront de manière séquentielle :
Grok > Date > GeoIP > User agent**

8- Pour tester le pipeline, cliquez sur "Add documents".

9- Dans l'onglet "Documents", fournissez ce document d'exemple pour les tests :

```
[
  {
    "_source": {
      "message": "212.87.37.154 - - [05/May/2099:16:21:15 +0000] \"GET /favicon.ico HTTP/1.1\" 200 3638 \"-\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\""
    }
  }
]
```
![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/7b15d9c9-ffba-4fd7-bf6b-74453edeba6c)

10- Cliquez sur "Execute pipeline" et vérifiez que le pipeline fonctionne comme prévu.

![testing output](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/494cd45a-a332-4d54-85fb-174bcbdca083)

Si tout semble correct, fermez le panneau, puis cliquez sur "Créer le pipeline".

11- Redirigez vers `Dev Tools`, et Créez un modèle d'index avec la prise en charge des flux de données (data stream).

```
PUT _index_template/my-data-stream-template
{
  "index_patterns": [ "logs-parse.logs-default*" ],
  "data_stream": { },
  "priority": 500
}
```
12- Indexez un document avec le pipeline que vous avez créé.
```
POST logs-parse.logs-default/_doc?pipeline=my-pipeline
{
  "message": "89.160.20.128 - - [05/May/2099:16:21:15 +0000] \"GET /favicon.ico HTTP/1.1\" 200 3638 \"-\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\""
}
```

13- Pour vérifier, effectuez une recherche dans le flux de données pour récupérer le document. 

La recherche suivante utilise filter_path pour ne renvoyer que la source du document.
```
GET logs-parse.logs-default/_search?filter_path=hits.hits._source

```

L'API renvoie le résultat suivant :
```
{
  "hits": {
    "hits": [
      {
        "_source": {
          "@timestamp": "2099-05-05T16:21:15.000Z",
          "http": {
            "request": {
              "referrer": "\"-\"",
              "method": "GET"
            },
            "response": {
              "status_code": 200,
              "body": {
                "bytes": 3638
              }
            },
            "version": "1.1"
          },
          "source": {
            "geo": {
              "continent_name": "Europe",
              "region_iso_code": "SE-AB",
              "city_name": "Sollentuna",
              "country_iso_code": "SE",
              "country_name": "Sweden",
              "region_name": "Stockholm County",
              "location": {
                "lon": 17.9653,
                "lat": 59.4284
              }
            },
            "ip": "89.160.20.128"
          },
          "message": "89.160.20.128 - - [05/May/2099:16:21:15 +0000] \"GET /favicon.ico HTTP/1.1\" 200 3638 \"-\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\"",
          "user": {
            "name": "-",
            "id": "-"
          },
          "url": {
            "original": "/favicon.ico"
          },
          "user_agent": {
            "original": "\"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\"",
            "os": {
              "name": "Mac OS X",
              "version": "10.11.6",
              "full": "Mac OS X 10.11.6"
            },
            "name": "Chrome",
            "device": {
              "name": "Mac"
            },
            "version": "52.0.2743.116"
          }
        }
      }
    ]
  }
}
```
