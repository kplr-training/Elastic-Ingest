# Ajout de Kafka au Setup 

Dans cette partie, vous allez changer la configuration de votre pipeline pour que vous aurez un résultat qui peut etre schématisé comme suit: 

![image (15)](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/1bc03880-aa41-4c1c-a582-8ba3eb475ea6)

Donc vous devez d'abord configurer une pipeline qui permet d'ingérer les données à partir de l'`Elastic Agent` et puis les transmettre vers le `Producer` qui va publier les données dans Kafka.

Ensuite, vous configurez une pipeline qui permet d'ingérer les données à partir du `Topic` et les envoyer vers ElasticSearch.

:warning: **AVANT TOUT** pour éviter tout souci ou conflit, assurez-vous qu'aucune pipelin logstash ne tourne
En pratique, cela est tout à fait possible. Mais pour ce faire, il faudrait qu'il n'y ait pas de conflits, ni au niveau des ports ni au niveau des repertoires de données et métadonnées. Pour faciliter les choses, on va donc se contenter de s'assurer que seules les pipelines logstash que nous allons configurer actuellement tournent (par exemple via `ps aux | grep logstash`)

## PARTIE 1: Pipeline Elastic Agent => Kafka

- Pour configurer cette pipeline, créez un fichier de configuration `elastic-agent-pipeline-kafka-output.conf`.
- Ce fichier va représenter une configuration basique de Logstash pour recevoir des données à partir d'Elastic Agent via le protocole Elastic Agent Input et les envoyer vers un topic Kafka spécifique. 

- Le fichier contient le code suivant:
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

output {
    kafka {
        bootstrap_servers => "localhost:9092"
        topic_id => ["test-topic"]
    }
}
```
- Maintenant, lancez la pipeline (Avant de la lancer, arretez tout processus qui utilise le port 5044):

```
/usr/share/logstash/bin/logstash -f /etc/logstash/elastic-agent-pipeline-kafka-output.conf
```
- **Gardez le Consumer lancé pour visualiser les données qui arrivent au Topic.**

⚠️ Lorsque vous avez créé votre agent policy, une intégration système a automatiquement été créée. 
Si c'est le cas, votre consumer doit afficher en continu les logs provenant de votre système ubuntu agent.

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/3918a15f-0a36-4047-b067-0d65e354070b)

````
2023-05-14T13:25:56.523Z {hostname=ip-172-30-2-214, os={kernel=5.15.0-1031-aws, codename=jammy, name=Ubuntu, type=linux, family=debian, version=22.04.2 LTS (Jammy Jellyfish), platform=ubuntu}, containerized=false, ip=[172.30.2.214, fe80::c5e:43ff:feda:6ee5], name=ip-172-30-2-214, id=f84cf21a72db4a088d1f52a60c9fcb4f, mac=[0E-5E-43-DA-6E-E5], architecture=x86_64} %{message}
2023-05-14T13:25:56.523Z {hostname=ip-172-30-2-214, os={kernel=5.15.0-1031-aws, codename=jammy, name=Ubuntu, type=linux, family=debian, version=22.04.2 LTS (Jammy Jellyfish), platform=ubuntu}, containerized=false, ip=[172.30.2.214, fe80::c5e:43ff:feda:6ee5], name=ip-172-30-2-214, id=f84cf21a72db4a088d1f52a60c9fcb4f, mac=[0E-5E-43-DA-6E-E5], architecture=x86_64} %{message}
2023-05-14T13:25:56.523Z {hostname=ip-172-30-2-214, os={kernel=5.15.0-1031-aws, codename=jammy, name=Ubuntu, type=linux, family=debian, version=22.04.2 LTS (Jammy Jellyfish), platform=ubuntu}, containerized=false, ip=[172.30.2.214, fe80::c5e:43ff:feda:6ee5], name=ip-172-30-2-214, id=f84cf21a72db4a088d1f52a60c9fcb4f, mac=[0E-5E-43-DA-6E-E5], architecture=x86_64} %{message}
2023-05-14T13:25:56.523Z {hostname=ip-172-30-2-214, os={kernel=5.15.0-1031-aws, codename=jammy, name=Ubuntu, type=linux, family=debian, version=22.04.2 LTS (Jammy Jellyfish), platform=ubuntu}, containerized=false, ip=[172.30.2.214, fe80::c5e:43ff:feda:6ee5], name=ip-172-30-2-214, id=f84cf21a72db4a088d1f52a60c9fcb4f, mac=[0E-5E-43-DA-6E-E5], architecture=x86_64} %{message}
2023-05-14T13:25:56.523Z {hostname=ip-172-30-2-214, os={kernel=5.15.0-1031-aws, codename=jammy, name=Ubuntu, type=linux, family=debian, version=22.04.2 LTS (Jammy Jellyfish), platform=ubuntu}, ip=[172.30.2.214, fe80::c5e:43ff:feda:6ee5], containerized=false, name=ip-172-30-2-214, id=f84cf21a72db4a088d1f52a60c9fcb4f, mac=[0E-5E-43-DA-6E-E5], architecture=x86_64, network={ingress={packets=35, bytes=6352}, egress={packets=39, bytes=27534}}} %{message}
````

- Ajoutez des logs de test dans le fichier `test.log` que vous avez créé préalablement:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/07cd4fdb-7cb7-41ac-bd4a-046e03b788d8)

- Vérifiez que le message existe bien dans le topic maintenant:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/ea8ba260-a295-4c65-b313-97e38932a122)

**Maintenant, on peut bien dire que les logs sont bien récupérés à partir du fichier de log et bien envoyés vers le topic de Kafka**

## PARTIE 2: Pipeline  Kafka => ElasticSearch

- Pour configurer cette pipeline, créez un fichier de configuration `elastic-agent-pipeline-kafka-input.conf`.

- Ce fichier va représenter une configuration de base de Logstash pour consommer des messages à partir d'un topic Kafka spécifique, ajouter un champ supplémentaire à chaque message à l'aide du filtre mutate, et publier les données traitées dans Elasticsearch. 

- Les données peuvent etre publiées dans un `index` ou bien un `data stream`.

#### Cas 1 : Publication dans un index

- Le fichier contient le code suivant:
```
input {
  kafka {
    bootstrap_servers => "localhost:9092"
    topics => ["test-topic"]
  }
}

filter {
  mutate {
    add_field => { "a_logstash_field" => "Hello, world!" }
  }
}

output {
  elasticsearch {
    hosts => "https://PRIVATE_IP_ADDRESS:9200"
    user=>"elastic"
    password=>"elastic"
    index => "kafka_data"
    ssl => true
    cacert => "/etc/logstash/config/certs/http_ca.crt"
  }
}
```
- Maintenant, lancez la pipeline:

```
/usr/share/logstash/bin/logstash -f /etc/logstash/elastic-agent-pipeline-kafka-input.conf --path.data /tmp
```

**NB: Notez l'ajout de `--path.data /tmp` à la fin de la commande.**
Cela a pour objet de fournir un repertoire de travail spécifique à la pipeline pour ne pas entrer en conflit avec l'autre pipeline précédemment lancée

- Insérez un nouveau message dans votre fichier de log et vérifiez tout d'abord qu'il a bien arrivé à votre topic:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/f9b4fa3b-3192-483a-8efc-7fd30af0a2ad)

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/65cbcad7-6c45-464f-83e9-50f7e69a90fd)

- Vérifiez que l'index `kafka-data` existe parmi la liste des indices existants:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/b1159824-7512-468d-998f-befb9f07ca4f)

- Les logs sont publiés dans l'index `kafka-data`, créez un data view pour visualiser son contenu:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/e04cbbef-f9cf-43a9-9845-ef7fa69ec640)

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/9af23705-447c-4e46-8a38-101c360684ff)

**Et Voilà! vous pouvez bien vérifiez que le message est bien stocké dans l'index et vous avez ainsi le champ `a_logstash_field` qui est ajouté** 

#### Cas 2 : Publication dans un datastream

- Le fichier contient le code suivant:
```
input {
  kafka {
    bootstrap_servers => "localhost:9092"
    topics => ["test-topic"]
  }
}

filter {
  mutate {
    add_field => { "a_logstash_field" => "Hello, world!" }
  }
}

output {
  elasticsearch {
    hosts => "https://IP-ADRESS:9200"
    api_key => "XXXXXXXXXX"
    data_stream => true
    data_stream_dataset => "kafka_output"
    ssl => true
    cacert => "/etc/logstash/config/certs/http_ca.crt"
  }
}
```

- Maintenant, lancez la pipeline:

```
/usr/share/logstash/bin/logstash -f /etc/logstash/elastic-agent-pipeline-kafka-input.conf 
```

**NB: Si la pipeline ne s'exécute pas, ajoutez `--path.data /tmp` à la fin de la commande**


- Insérez un nouveau message dans votre fichier de log et vérifiez qu'il est publié dans le topic:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/9ba53801-2655-45a1-a0da-c4bed29dfd59)

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/30a6b6ac-f5ab-4a90-a27d-e71c0808b9a2)

- Vérifiez que le data stream `kafka_output` est bien créé: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/fd1f3831-571b-455b-b462-5bac90ee359b)

- Créez un data view pour visualiser le contenu du data stream :

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/38c93420-6d34-4f59-86d1-cd12709dab52)

- Vérifiez que votre message existe bien dans le data stream et que  le champ `a_logstash_field` est ajouté:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/e6a74035-0816-4627-ae81-fdb2bf8d133e)

**Et Voilà! vous pouvez bien vérifiez que le message est bien stocké dans le datastream et vous avez ainsi le champ `a_logstash_field` qui est ajouté** 
