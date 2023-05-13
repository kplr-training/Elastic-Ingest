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

```
`Processor` : `Grok`

`Field` : `message`

`Patterns` : `%{IPORHOST:a_source_ip}#%{USER:a_city}#%{USER:a_country}`

```
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

## Logstash Pipeline

Une pipeline Logstash se compose de trois étapes principales : l'étape d'entrée, qui récupère les données, l'étape de filtrage, où les données sont transformées et enrichies, et l'étape de sortie, qui détermine où les données traitées seront envoyées. 

Chaque étape utilise des plugins configurables pour effectuer des opérations spécifiques sur les données. La configuration de la pipeline est définie dans un fichier de configuration, et une fois la pipeline lancée, Logstash traite continuellement les données selon cette configuration. 

Les pipelines Logstash offrent une grande flexibilité pour répondre aux besoins spécifiques de collecte, de transformation et de livraison des données.

Voici un schéma qui explique l'architecture du setup que vous allez créer pour ingérer et traiter les logs existants dans la machine qui contient l'Elastic Agent:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/3e6b69e4-1f2a-40cd-ad7c-85e9fe285553)

**- Création d'une pipeline logstash**

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



**NB: Avant d'exécuter la pipeline, vérifiez bien qu'aucune autre pipeline est entrain de s'exécuter. Pour ce faire, vous vérifiez s'il y a un service qui utilise le port de Logstash '5044' à l'aide de la commande suivante:**

```
sudo lsof -i :5044
```

S'il existe un processus qui s'excécute, récupérez sans `PID` et tapez la commande suivante pour le terminer:

```
kill -9 YOUR-PID
```

- Pour tester votre pipeline, vous devez tout d'abord l'exécuter en utilisant la commande suivante: 

```
/usr/share/logstash/bin/logstash -f /path/to/your/configuration/file
```

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
