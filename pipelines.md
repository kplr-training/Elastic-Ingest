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


- Ensuite, vous allez mentionné le chemin vers le fichier qui contient vos logs. Dans votre cas, vous allez créez un fichier log de test dans lequel vous allez ajouté des logs provisoires. 

Pour ce faire, créez le fichier suivant dans le répertoire `/var/log`:

 ```
 vi test.log
 ```
 - Puis, copiez le chemin vers le fichier dans le champ `Log file path`: 

- Dans la partie `Advanced options`, ajoutez le nom de votre dataset: 

- Finalement, vous précisez la policy auquel l'intégration va etre ajoutée. 


- Pour vérifiez que tout marche bien, créez un `Data View` pour pouvoir visualiser les logs ingérés. Pour ce faire, redirigez vers `Data Views` dans le menu `Stack Management` puis cliquez `Create data view`. 


- Maintenant, redirigez vous vers `Analytics` > `Discover` 



- En haut à gauche, choissisez le Data view que vous venez de créer. Vous aurez un résultat comme le suivant: 




## Logstash Pipeline

Une pipeline Logstash se compose de trois étapes principales : l'étape d'entrée, qui récupère les données, l'étape de filtrage, où les données sont transformées et enrichies, et l'étape de sortie, qui détermine où les données traitées seront envoyées. 

Chaque étape utilise des plugins configurables pour effectuer des opérations spécifiques sur les données. La configuration de la pipeline est définie dans un fichier de configuration, et une fois la pipeline lancée, Logstash traite continuellement les données selon cette configuration. 

Les pipelines Logstash offrent une grande flexibilité pour répondre aux besoins spécifiques de collecte, de transformation et de livraison des données.

**Création d'une pipeline logstash**

Pour créer une pipeline logstash, vous créez un fichier de configuration pour votre pipeline Logstash. 

Ce fichier utilisera le langage de configuration Logstash (LSL). Vous pouvez le nommer comme vous le souhaitez, par exemple "my-pipeline.conf".

Dans votre fichier de configuration, ajoutez le code suivant qui configure une pipeline logstash:

```
input {
  elastic_agent {
    port => 5044
    ssl => true
    ssl_certificate_authorities => ["/etc/logstash/config/certs/ca/ca.crt"]
    ssl_certificate => "/etc/logstash/config/certs/logstash/logstash.crt"
    ssl_key => "/etc/logstash/config/certs/logstash/logstash.pkcs8.key"
    ssl_verify_mode => "force_peer"
  }
}

filter {
  mutate {
    add_field => { "a_logstash_field" => "Hello, world!" }
  }
}

output {
  elasticsearch {
    hosts => "https://X.X.X.X:9200"
    api_key => "XXXXXXXXXXXXXXXXXXXXXXX"
    data_stream => true
    ssl => true
    cacert => "/etc/logstash/config/certs/http_ca.crt"
  }
}
```
Cette pipeline utilise l'entrée Elastic Agent pour recevoir des événements via le port 5044 avec une connexion SSL sécurisée. 

Les certificats SSL nécessaires sont spécifiés, y compris l'autorité de certification (CA) pour vérifier le certificat du client. 

Dans l'étape de filtrage, le plugin "mutate" est utilisé pour ajouter un champ supplémentaire appelé "a_logstash_field" avec la valeur "Hello, world!". 

Enfin, dans l'étape de sortie, les événements sont envoyés vers Elasticsearch en utilisant l'URL spécifiée avec une clé API pour l'authentification. Les données sont envoyées en tant que data stream, avec une connexion SSL sécurisée, et un certificat CA est spécifié pour la vérification.