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
