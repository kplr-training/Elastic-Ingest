## What have been said !

- L'output vers lequel les agents vont envoyer les données dans leurs cas c'est : Logstash
- L'output est sécurisé par un certificat SSL
- Le pipeline utilisé est le pipeline par défaut configuré sur Logstash.
- le fichier `logstash.yml` (existe dans le répertoire `logstash/config`) contient la configuration de Logstash pour utiliser les comptes par défaut et la partie de sécurité TLS préconfigurée.
- le fichier `pipelines.yml` (existe dans le répertoire `logstash/config`) contient le path vers le fichier de configuration de la pipeline logstash.
- le fichier de configuration de la pipeline logstash `pipeline.conf` (existe dans le répertoire `logstash/pipeline`) définit une pipeline classique avec des inputs et des filters ( dans leur cas: 1 input , 2 filter).
- l'agent elastic est configuré sur beats?? (pas assez clair)
- L'integration créé est de type Custom log
- La version de windows utilisé est windows server 2020
## La configuration de l'intégration Custom Logs

Pour configurer le custom logs, on précise le chemin vers le fichier des logs:

![image](https://user-images.githubusercontent.com/123748177/234714625-9833b979-cbfa-4d1c-ae9a-4268850ab049.png)

Dans Constum configuration, on ajoute une gestion de Multiline vu que un log n'est pas présenté dans une seule ligne mais dans plusieurs lignes. 

![image](https://user-images.githubusercontent.com/123748177/234717521-1f3abb45-95c4-469c-8443-2dc7803cd9ee.png)


Donc, on ajoute une configuration pour reconstruire une seule ligne de log à partir des informations du log trouvé entre `#` et `#end`.

![image](https://user-images.githubusercontent.com/123748177/234715090-c8580e84-ec14-4dd9-9c10-cee18c26e70a.png)

## Back to what have been said !

- La configuration de l'intégration se fait dans Kibana et par la suite l'intégration est définit dans une policy.
- le Fleet server va chercher l'intégration sur le serveur Elastic et la met en disposition des agents qui sont associés à la policy correspondante de l'intégration.
- Les agents cherchent les policies dans le fleet server.
- Un agent peut etre associé à une seule policy.
- Les agents cherchent tous seuls les intégrations configurés dans Elastic pour la policy correspondante.
- RIEN à FAIRE SUR L'AGENT!!
- Les données sont envoyés sur l'output configuré pour etre par la suite ingérer dans Elasticsearch.
- L'output peut etre soit un serveur Elastic soit un serveur Logstash.
- On peut avoir plusieurs outputs. 

## La configuration de Logstash Output

- On choisit comme type d'output: logstash

![image](https://user-images.githubusercontent.com/123748177/234723165-47bf7e64-07c8-4eba-b67d-ed2964350c37.png)

- On doit ajouter une configuration additionelle pour la pipeline, voilà l'exemple proposé: 

![image](https://user-images.githubusercontent.com/123748177/234723444-25284409-6191-44da-98d4-f59f73157aa2.png)
![image](https://user-images.githubusercontent.com/123748177/234723488-eb69e4e1-4fd0-4714-9533-3ce50490f0cc.png)

- Il faut aussi injecter les certificats SSL , on crée un certificat pour tous les noeuds du cluster.
- La première est la clé publique de la certificat et la deuxième est la clé privée.

![image](https://user-images.githubusercontent.com/123748177/234723596-5859b2f7-7410-4354-b950-d798392b74c5.png)

## Les étapes séquentielles à suivre pour la mise en place du projet

- Installez la couche système (VM, Docker, etc...)
- Installez Elasticsearch ( Single node or cluster)
- Installez Kibana
- Installez Logstash
- Installez et configurez le Fleet server
- Déployez des Elastic Agents
- Configurez d'abord les outputs (nécessaires pour créer des intégrations)
- Créez des policies
- Créez les pipelines si besoin :
        * Si vous avez logstash comme output vous ne serez pas besoin de créer un pipeline vu qu'il est déjà configuré dans logstash.

        * Sinon vous créez un pipeline Elastic.

        * il est recommandé d'ajouter le pipeline dans la partie `ingest pipelines` dans la configuration de l'intégration
        
- Créez les instances des intégrations et les mettez dans les policies ( peuvent etre personnalisés (custom))

## Remarques

- Logstash sert à minimiser la charge qui peut influencer le serveur Elasticsearch s'il est en communication direct avec les agents.
- On peut ajouter Kafka entre Logstash et les Elastic agents pour gérer la charge.
- Elastic agent ne sait pas utilisé Kafka comme output! il nécessite Logstash ou ELastic.
- Pour ajouter Kafka, on doit ajouter tout d'abord un serveur Logstash qui va etre utiliser seulement pour pointer vers Kafka qui pointe par la suite vers un autre serveur Logstash.
