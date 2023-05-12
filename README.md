# Elastic-Ingest

## Description du contenu des fichiers

### Installation-elastic-kibana-logstash.md

- Installation et configuration Elasticsearch
- Installation et configuration Kibana
- Installation et configuration Logstash

### Configure Kibana from UI.md

- Configuration et enrollement d'Elasticsearch dans Kibana

### custom-logs-ingestion-pipeline.md

Ce fichier contient la documentation d'un test de création d'un pipeline d'ingestion suivant le tutoriel de John Lai : [Voici le lien du tutoriel](https://www.youtube.com/watch?v=0_X45R1mVnI)

- Création d'un Ingest Pipeline
- Création d'une intégration de type `Custom Windows Event Logs`
- Liaison de la pipeline avec l'intégration
- Visualisation du contenu du dataset créé

### enrich-data.md

Ce fichier contient la documentation d'un test d'enrichissement des données suivant le tutoriel de Evermight : [Enrich Data with Elasticsearch 8.x - Part 1: Basic Examples](https://www.youtube.com/watch?v=VCSgTtU4ddI)

### fleet-server.md

- Ajout et configuration d'un Fleet server

### kafka-pipeline.md

- Installation Kafka
- Exécution du Consumer et Producer
- Ajout du Kafka au pipeline existant
- Publication des données dans un index et un datastream

### logstash-output.md

- Configuration d'un output logstash
- Création d'autorité de certification et signature des certificats SSL de Client et de Logstash server

### meeting-result.md

- Notes qui résument les points essentielles dans la réunion avec le client

### network-security-logs.md

- code de génération des logs aléatoires (NtworkSecurity)

### pipelines.md

- Création d'une nouvelle intégration de type `Custom logs`
- Création et test d'un Ingest Pipeline
- Création et test d'un pipeline Logstash
- Ajout et test des Filebeat processors

### policies-integration.md

- Procédure de création d'une Agent Policy
- Activation de license
- Ajout d'un Custom Windows Event Logs Integration
- Création d'un data view
- Visualisation du data view

########################################################################

- Presentation Intégrations
- Faire des graphes d'intégration (serveurs, composants, etc.)
- Screenshots + Menu Guide (A>B>C)
- Ordre d'installation : Stack ES  > Kibana > Fleet Server > Agent 
- Add security in the end https://www.elastic.co/guide/en/fleet/current/secure.html
- Add Integration Creation (Brief summary of internal structure of an integration package)
- You can install only a single Elastic Agent per host.
- Uninstall agent from host
- Elastic ingest pipeline
- OR logstash pipeline
- one elastic agent can be configured to have many integrations 
- An integration can be added to only one policy, a policy can be tied to many integrations
- Many agents can be enrolled to a policy
- Each Elastic Agent can only be enrolled in a single policy.
- Agent policies are used to manage settings across a group of agents. 
- You can add integrations to your agent policy to specify what data your agents collect.
- Policy need to be linked to a fleet server host
- https://www.elastic.co/guide/en/fleet/current/agent-policy.html#change-policy-output
- Configuration des outputs : Fleet > settings > outputs
- By default, each policy enables the system integration, which collects system information and metrics.
- Each integration class has one common set of assets ( dashboards, viz, etc)
- Fleet server policy is a special agent policy because fleet server itself is a specific elastic agent. 
