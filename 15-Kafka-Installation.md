# Ajout de Kafka dans le setup : Installation

Kafka est un système de messagerie distribué qui permet de gérer efficacement les flux de données en temps réel. 

Dans un pipeline d'ingestion des logs, Kafka joue un rôle clé grâce à ses fonctionnalités principales. Il permet la collecte centralisée des logs en provenance de différentes sources, offrant une file d'attente persistante et évolutive. 

Kafka garantit la scalabilité et la résilience, capable de traiter de grands volumes de logs simultanément. Il permet également le traitement en temps réel des logs grâce à des mécanismes de publication et d'abonnement. 

Les logs stockés dans Kafka sont durables, assurant leur persistance en cas de défaillance des systèmes de traitement. De plus, Kafka s'intègre facilement avec d'autres composants de la stack Elastic, facilitant la communication entre les différentes étapes du pipeline d'ingestion. 

Vous allez créer un pipeline qui s'organise comme suit:

![image (15)](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/1bc03880-aa41-4c1c-a582-8ba3eb475ea6)

## Installation Kafka

**:warning: l'installation de Kafka se fait dans les deux machines: Fleet server, et Elastic agent**


- Installez Java maintenant:

```
apt-get update && apt install -y openjdk-11-jre-headless
```

- Vérifiez avec la commande suivante que Java est bien installé:
```
java --version
```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/dd4d128c-5f07-4916-8c5d-3494c0062422)


- Afin d'installer Kafka, téléchargez le package confluent 

```
curl -O https://packages.confluent.io/archive/7.4/confluent-7.4.0.tar.gz
```

- Décompressez l'archive : 

```
tar xzf confluent-7.4.0.tar.gz
```

- configurez le path (rajouter au bashrc et sourcer)

```
export CONFLUENT_HOME=/root/confluent-7.4.0
export PATH=$PATH:$CONFLUENT_HOME/bin
```

<details><summary>
  <sub>installation manuelle (non nécessaire)</sub></summary> 
  
 - Plutôt que d'utiliser le package confluent, Vous pouvez sinon télécharger directement la version binaire de Kafka en utilisant la commande suivante: 

```
wget https://archive.apache.org/dist/kafka/2.8.2/kafka_2.12-2.8.2.tgz
```

- Vous aurez comme résultat un fichier .tgz qui contient la version binaire de Kafka, utilisez la commande suivante pour l'extraire:

```
tar -xzf kafka_2.12-2.8.2.tgz
```

- Déplacez le bin vers le répertoire `/opt/kafka`

```
sudo mv kafka_2.12-2.8.2 /opt/kafka
```
</details>


## Exécution du Consumer et Producer

Selon le schéma de ce setup, le Producer qui permet de produire et de publier les données dans Kafka sera dans votre  l'Elastic Agent qui récupére les données à partir des logs et les envoie vers Kafka.

Le Consumer, qui lit et traite les messages à partir d'un ou plusieurs topics dans un cluster Kafka, sera la machine qui contient Fleet server qui lit les données depuis le topic de Kafka, les traite, et les envoie vers Elasticsearch.

### 1- Consumer

#### Lancement du Serveur Kafka


- Dans la machine où vous avez le Fleet server, vous allez démarrer le serveur ZooKeeper dans un environnement Kafka. Pour ce faire, tapez la commande suivante: 

```
confluent local services kafka start
```

<details><summary>
  <sub>lacement manuel (non nécessaire)</sub></summary> 

Starting zookeper
```
bin/zookeeper-server-start.sh config/zookeeper.properties > zk.log &
``` 

cette commande lance le serveur ZooKeeper en utilisant la configuration spécifiée dans config/zookeeper.properties, redirige la sortie vers le fichier zk.log et exécute le processus en arrière-plan pour assurer le bon fonctionnement de ZooKeeper dans l'environnement Kafka.

- Maintenant, vous démarrez Kafka:

```
kafka-server-start config/server.properties > ks.log &
```
 </details>

### Création du topic

Un topic dans Apache Kafka est une catégorie ou un flux de messages qui peut être publié et consommé. C'est une unité logique de regroupement de données dans un cluster Kafka. 

Un topic peut être considéré comme un canal de communication où les producteurs envoient des messages et les consommateurs les lisent.

- Pour créer un topic, vous tapez la commande suivante :
```
kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test-topic
```

Nota : Par défaut, la création d'un topic est automatique lors de l'écriture dans un topic via le console producer. 
Cette étape est donc facultative.

- Pour vérifier que le topic est bien créé, listez les topics existants:
```
kafka-topics --list --bootstrap-server localhost:9092
```

### Lancement du Consumer

- Maintenant, vous lancez votre `Consumer` pour consommer les messages à partir du topic que vous avez créé dans Apache Kafka en utilisant la console.
```
kafka-console-consumer --topic test-topic --from-beginning --bootstrap-server localhost:9092
```

**Lorsque des nouvelles données sont stocké dans le topic, vous allez les visualiser dans la console qui exécute cette commande**

### 2- Producer

#### Lancement du Producer

- Maintenant, vous allez produire des données à l'aide du producer pour vérifier que Kafka fonctionne correctement. Pour ce faire, tapez cette commande en ajoutant votre adresse IP interne:  

```
kafka-console-producer --broker-list YOUR-PRIVATE-IP-ADRESS:9092 --topic test-topic
```

- Ensuite saisissez des messages aléatoires pour tester et visualisez le résultat dans le consumer:

**Producer**

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/be6a42d9-f088-4fed-98ea-4d1022ceb33f)

**Consumer**

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/087373f0-37bb-41b2-a6e8-7a6868b05b98)


**Si vous voyez comme ces résultats, donc félicitations! Kafka fonctionne correctement!**

