## Utilisation de Logstash Output 

Une pipeline Logstash se compose de trois étapes principales : l'étape d'entrée, qui récupère les données, l'étape de filtrage, où les données sont transformées et enrichies, et l'étape de sortie, qui détermine où les données traitées seront envoyées. 

Chaque étape utilise des plugins configurables pour effectuer des opérations spécifiques sur les données. La configuration de la pipeline est définie dans un fichier de configuration, et une fois la pipeline lancée, Logstash traite continuellement les données selon cette configuration. 

Les pipelines Logstash offrent une grande flexibilité pour répondre aux besoins spécifiques de collecte, de transformation et de livraison des données.

Voici un schéma qui explique l'architecture du setup que vous allez créer pour ingérer et traiter les logs existants dans la machine qui contient l'Elastic Agent:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/3e6b69e4-1f2a-40cd-ad7c-85e9fe285553)

**- Création d'une pipeline logstash**

⚠️ vous pouvez passer cette étape si vous avez déja configuré un output logstash précédemment ⚠️

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

### Configuration de l'output de la policy vers Logstash

Selectionnez votre policy

![Screenshot 2023-05-13 at 13 14 08](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/4465fc4d-f1a5-46d7-a7d0-71d821e7b00f)

Selectionnez l'onglet settings

![Settings](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/f0a041c6-c1e0-40bd-b7b2-8803ffdfe542)

- Vérifiez bien que la policy, avec laquelle votre intégration est liée, a comme Output `Logstash Output`
(surligné en jaune)

![Image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/efa4863c-d4b5-4177-b26d-6359b5e2db87)

- Pour pouvoir utiliser l'Output de type Logstash, vous devez avoir une licence.
- Autrement, l'option logstash est disponible mais grisée : 

![grayedout](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/ebaaf97b-3a9a-411d-a272-007967679068)

- Pour ce faire, redirigez vous vers Dev Tools et tapez la commande suivante:

```GET /_license/trial_status?pretty```

- Cela vous renvoie le retour suivant : 

```
{
  "eligible_to_start_trial": true
}
```

- Activez la license : 

```POST /_license/start_trial?acknowledge=true&pretty```

- Cela vous renvoie le retour suivant : 

```
{
  "acknowledged": true,
  "trial_was_started": true,
  "type": "trial"
}
```


- Puis, vous pouvez vérifier que vous avez obtenu la licence avec la commande suivante:

```GET /_license/trial_status?pretty```

- Cela vous renvoie le retour suivant : 

```
{
  "eligible_to_start_trial": false
}
```

- A présent vous pouvez retourner sur le setting de la policy et selectionner l'output logstash.

- Si vous avez bien configuré le fichier `pipelines.yml` de logstash comme il se doit, il suffit de lancer le service logstash

`service logstash start`

### => A présent, toutes les intégrations rattachées à l'agent policy en question seront redirigées automatiquement en bout de chaîne vers l'output logstash.

- Maintenant, vous allez ajouter une autre ligne de log pour tester que la pipeline logstash fonctionne correctement:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/d491179b-40c0-4b08-afa0-d3d005c29fe5)

- Vérifiez votre Data view maintenant, vous constatez bien que vous avez un nouveau document inséré: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/e6806427-fe74-4500-bd63-aa8bf9126c21)

- Développez le document et vérifiez que le champ `a_logstash_field` existe dans le document: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/4e72a915-80fd-4767-a670-d3bd2c22266f)
