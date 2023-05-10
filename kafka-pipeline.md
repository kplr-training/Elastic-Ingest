# Ajout du Kafka dans le setup

Kafka est un système de messagerie distribué qui permet de gérer efficacement les flux de données en temps réel. 

Dans un pipeline d'ingestion des logs, Kafka joue un rôle clé grâce à ses fonctionnalités principales. Il permet la collecte centralisée des logs en provenance de différentes sources, offrant une file d'attente persistante et évolutive. 

Kafka garantit la scalabilité et la résilience, capable de traiter de grands volumes de logs simultanément. Il permet également le traitement en temps réel des logs grâce à des mécanismes de publication et d'abonnement. 

Les logs stockés dans Kafka sont durables, assurant leur persistance en cas de défaillance des systèmes de traitement. De plus, Kafka s'intègre facilement avec d'autres composants de la stack Elastic, facilitant la communication entre les différentes étapes du pipeline d'ingestion. 

Vous allez créer un pipeline qui s'organise comme suit:

![image (15)](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/1bc03880-aa41-4c1c-a582-8ba3eb475ea6)

## Installation Kafka

- Afin d'installer Kafka, téléchargez la version binaire de Kafka en utilisant la commande suivante: 

```
wget https://archive.apache.org/dist/kafka/2.8.2/kafka_2.12-2.8.2.tgz
```

**NB: l'installation de Kafka se fait dans les deux machines: Fleet server, et Elastic agent**

- Vous aurez comme résultat un fichier .tgz qui contient la version binaire de Kafka, utilisez la commande suivante pour l'extraire:

```
tar -xzf kafka_2.12-2.8.2.tgz
```

- Déplacez le bin vers le répertoire `/opt/kafka`

```
sudo mv kafka_2.12-2.8.2 /opt/kafka
```

- Installez Java maintenant:

```
apt install openjdk-11-jre-headless
```

- Vérifiez avec la commande suivante que Java est bien installé:
```
java --version
```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/dd4d128c-5f07-4916-8c5d-3494c0062422)

## Exécution du Consumer et Producer

Selon le schéma de ce setup, le Producer qui permet de produire et de publier les données dans Kafka sera dans votre  l'Elastic Agent qui récupére les données à partir des logs et les envoie vers Kafka.

Le Consumer, qui lit et traite les messages à partir d'un ou plusieurs topics dans un cluster Kafka, sera la machine qui contient Fleet server qui lit les données depuis le topic de Kafka, les traite, et les envoie vers Elasticsearch.

### 1- Consumer

#### - Lancement du serveur

- Dans la machine où vous avez le Fleet server, vous allez démarrer le serveur ZooKeeper dans un environnement Kafka. Pour ce faire, tapez la commande suivante: 

```
bin/zookeeper-server-start.sh config/zookeeper.properties > zk.log &
```

cette commande lance le serveur ZooKeeper en utilisant la configuration spécifiée dans config/zookeeper.properties, redirige la sortie vers le fichier zk.log et exécute le processus en arrière-plan pour assurer le bon fonctionnement de ZooKeeper dans l'environnement Kafka.

- Maintenant, vous démarrez Kafka:

```
bin/kafka-server-start.sh config/server.properties > ks.log &
```

#### - Création du topic

Un topic dans Apache Kafka est une catégorie ou un flux de messages qui peut être publié et consommé. C'est une unité logique de regroupement de données dans un cluster Kafka. 

Un topic peut être considéré comme un canal de communication où les producteurs envoient des messages et les consommateurs les lisent.

- Pour créer un topic, vous tapez la commande suivant dans le répertoire `/opt/kafka`:
```
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test-topic
```

- Pour vérifier que le topic est bien créé, listez les topics existants:
```
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

#### - Lancement du Consumer

- Maintenant, vous lancez votre `Consumer` pour consommer les messages à partir du topic que vous avez créé dans Apache Kafka en utilisant la console.
```
bin/kafka-console-consumer.sh --topic test-topic --from-beginning --bootstrap-server localhost:9092
```

**Lorsque des nouvelles données sont stocké dans le topic, vous allez les visualiser dans la console qui exécute cette commande**

### 2- Producer

#### Lancement du producer

- Maintenant, vous allez produire des données à l'aide du producer pour vérifier que Kafka fonctionne correctement. Pour ce faire, tapez cette commande en ajoutant votre adresse IP interne:  

```
bin/kafka-console-producer.sh --broker-list YOUR-PRIVATE-IP-ADRESS:9092 --topic test-topic
```

- Ensuite saisissez des messages aléatoires pour tester et visualisez le résultat dans le consumer:

**Producer**

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/be6a42d9-f088-4fed-98ea-4d1022ceb33f)

**Consumer**

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/087373f0-37bb-41b2-a6e8-7a6868b05b98)


**Si vous voyez comme ces résultats, donc félicitations! Kafka fonctionne correctement!**

## Ajout du Kafka au setup 

Dans cette partie, vous allez changer la configuration de votre pipeline pour que vous aurez un résultat qui peut etre schématisé comme suit: 

![image (15)](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/1bc03880-aa41-4c1c-a582-8ba3eb475ea6)

Donc vous devez d'abord configuré une pipeline qui permet d'ingérer les données à partir de l'`Elastic Agent` et puis les transmettre vers le `Producer` qui va publier les données dans Kafka.

Ensuite, vous configurez une pipeline qui permet d'ingérer les données à partir du `Topic` et les envoyer vers ElasticSearch.

### Pipeline Elastic Agent --> Kafka

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

- Ajoutez des logs de test dans le fichier `test.log` que vous avez créé préalablement:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/07cd4fdb-7cb7-41ac-bd4a-046e03b788d8)

- Vérifiez que le message existe bien dans le topic maintenant:

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/ea8ba260-a295-4c65-b313-97e38932a122)

