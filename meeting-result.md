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
