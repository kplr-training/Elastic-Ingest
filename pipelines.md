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

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/9468fd4b-0f82-4200-acb6-38e870305541)

- Ensuite, vous allez mentionné le chemin vers le fichier qui contient vos logs. Dans votre cas, vous allez créez un fichier log de test, dans lequel vous allez ajouté des logs provisoires, dans la machine qui contient votre `Elastic Agent`. 

Pour ce faire, créez le fichier suivant dans le répertoire `/var/log`:

 ```
 vi test.log
 ```
 - Puis, copiez le chemin vers le fichier dans le champ `Log file path`: 

![configure integration 1](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/e402153f-3d69-40fd-91ce-31004ff4132f)

- Dans la partie `Advanced options`, ajoutez le nom de votre dataset: 

![configure log file](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/fe8a592b-acfc-4092-882a-2a361b131d06)

- Finalement, vous précisez la policy auquel l'intégration va etre ajoutée. 

![policy](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/d63c43bd-862f-4999-b941-54a99ec8e5f1)

- Pour vérifiez que tout marche bien, créez un `Data View` pour pouvoir visualiser les logs ingérés. Pour ce faire, redirigez vers `Data Views` dans le menu `Stack Management` puis cliquez `Create data view`. 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/c54e621c-767b-48bf-a2c0-36ef5bad1dc6)

- Pour pouvoir visualiser ce Data view, vous devez avoir des données dans votre fichier de logs. Insérez une ligne de log dans le fichier `test.log`

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/c52c957c-7ff6-4641-8e97-59eff456deac)

- Maintenant, redirigez vous vers `Analytics` > `Discover`. En haut à gauche, choissisez le Data view que vous venez de créer. Vous aurez un résultat comme le suivant: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/5cd4b594-1173-4c8f-b551-7262629daed3)

Si vous développez le document inséré, vous pouvez bien voir qu'il contient le message de log que vous avez saisi:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/590ff8e2-dcfa-44e8-975c-42016c999082)


## Logstash Pipeline

Une pipeline Logstash se compose de trois étapes principales : l'étape d'entrée, qui récupère les données, l'étape de filtrage, où les données sont transformées et enrichies, et l'étape de sortie, qui détermine où les données traitées seront envoyées. 

Chaque étape utilise des plugins configurables pour effectuer des opérations spécifiques sur les données. La configuration de la pipeline est définie dans un fichier de configuration, et une fois la pipeline lancée, Logstash traite continuellement les données selon cette configuration. 

Les pipelines Logstash offrent une grande flexibilité pour répondre aux besoins spécifiques de collecte, de transformation et de livraison des données.

**Création d'une pipeline logstash**

Pour créer une pipeline logstash, vous créez un fichier de configuration pour votre pipeline Logstash. 

Ce fichier utilisera le langage de configuration Logstash (LSL). Vous pouvez le nommer comme vous le souhaitez, par exemple "my-pipeline.conf".

- Dans votre fichier de configuration, ajoutez le code suivant qui configure une pipeline logstash:

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

Enfin, dans l'étape de sortie, les événements sont envoyés vers Elasticsearch en tant que data stream en utilisant l'URL spécifiée avec une clé API pour l'authentification. 

- Pour tester votre pipeline, vous devez tout d'abord l'exécuter en utilisant la commande suivante: 

```
/usr/share/logstash/bin/logstash -f /path/to/your/configuration/file
```

**NB: Vérifiez bien qu'aucune autre pipeline est entrain de s'exécuter**

- Vérifiez bien que la policy, avec laquelle votre intégration est liée, a comme Output `Logstash Output`

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/efa4863c-d4b5-4177-b26d-6359b5e2db87)

- Maintenant, vous allez ajouter une autre ligne de log pour tester que la pipeline logstash fonctionne correctement:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/d491179b-40c0-4b08-afa0-d3d005c29fe5)

- Vérifiez votre Data view maintenant, vous constatez bien que vous avez un nouveau document inséré: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/e6806427-fe74-4500-bd63-aa8bf9126c21)

- Développez le document et vérifiez que le champ `a_logstash_field` existe dans le document: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/4e72a915-80fd-4767-a670-d3bd2c22266f)


## Integration Processors

L'avantage d'utiliser des processeurs d'intégration est qu'ils permettent de traiter les données en amont, directement lors de l'indexation dans Elasticsearch, évitant ainsi les étapes de post-traitement coûteuses. Cela garantit une ingestion plus rapide et une utilisation efficace des ressources.

## Ajout d'un simple processor

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

