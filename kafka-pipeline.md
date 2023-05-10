## Ajout du Kafka dans le setup

Kafka est un système de messagerie distribué qui permet de gérer efficacement les flux de données en temps réel. 

Dans un pipeline d'ingestion des logs, Kafka joue un rôle clé grâce à ses fonctionnalités principales. Il permet la collecte centralisée des logs en provenance de différentes sources, offrant une file d'attente persistante et évolutive. 

Kafka garantit la scalabilité et la résilience, capable de traiter de grands volumes de logs simultanément. Il permet également le traitement en temps réel des logs grâce à des mécanismes de publication et d'abonnement. 

Les logs stockés dans Kafka sont durables, assurant leur persistance en cas de défaillance des systèmes de traitement. De plus, Kafka s'intègre facilement avec d'autres composants de la stack Elastic, facilitant la communication entre les différentes étapes du pipeline d'ingestion. 

Vous allez créer un pipeline qui s'organise comme suit:

![image (15)](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/1bc03880-aa41-4c1c-a582-8ba3eb475ea6)

## Installation Kafka et exécution du Consumer et Producer

- Afin d'installer Kafka, téléchargez la version binaire de Kafka en utilisant la commande suivante: 

```
wget https://archive.apache.org/dist/kafka/2.8.2/kafka_2.12-2.8.2.tgz
```

**NB: l'installation de Kafka se fait dans les deux machines: Fleet server, et Elastic agent**

- Vous aurez comme résultat un fichier .tgz qui contient la version binaire de Kafka, utilisez la commande suivante pour l'extraire:

```
tar -xzf kafka_2.12-2.8.2.tgz
```

- Déplacez le bin vers le répertoie `/opt/kafka`

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