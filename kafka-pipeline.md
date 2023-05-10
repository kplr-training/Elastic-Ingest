## Ajout du Kafka dans le setup

Kafka est un système de messagerie distribué qui permet de gérer efficacement les flux de données en temps réel. 

Dans un pipeline d'ingestion des logs, Kafka joue un rôle clé grâce à ses fonctionnalités principales. Il permet la collecte centralisée des logs en provenance de différentes sources, offrant une file d'attente persistante et évolutive. 

Kafka garantit la scalabilité et la résilience, capable de traiter de grands volumes de logs simultanément. Il permet également le traitement en temps réel des logs grâce à des mécanismes de publication et d'abonnement. 

Les logs stockés dans Kafka sont durables, assurant leur persistance en cas de défaillance des systèmes de traitement. De plus, Kafka s'intègre facilement avec d'autres composants de la stack Elastic, facilitant la communication entre les différentes étapes du pipeline d'ingestion. 

Vous allez créer un pipeline qui s'organise comme suit:

