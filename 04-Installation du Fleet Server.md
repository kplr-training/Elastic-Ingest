## Création et configuration d'un Fleet Server

Fleet Server est un composant qui connecte les agents Elastic à Fleet. 

Il prend en charge de nombreuses connexions d'agents Elastic et sert de plan de contrôle pour mettre à jour les politiques des agents, collecter des informations sur leur état et coordonner les actions entre les agents Elastic. 

Il offre également une architecture évolutive. À mesure que la taille de votre déploiement d'agents augmente, vous pouvez déployer des serveurs Fleet supplémentaires pour gérer la charge de travail accrue.

## Ajout d'un Fleet Server

Pour créer un Fleet Server, redirigez vous vers `Management` > `Fleet` > `Agents` et cliquez `Add Fleet Server`.

![image (3)](https://user-images.githubusercontent.com/123748177/235185808-f9ab69d5-fcda-4239-b179-b9bdb6e80f24.png)

Maintenant, donnez un nom à votre Fleet server et puis saisissez l'URL vers la machine qui va contenir votre Fleet server:

⚠️ Faites attention à bien renseigner le protocole sécurisé HTTPS ains que votre Adresse IP Publique et le port 8220 ⚠️ <br>
(dans le cas présent l'adresse IP publique est celle par laquelle vous accédez à kibana)

![image (4)](https://user-images.githubusercontent.com/123748177/235185833-4b055fa0-77f6-44ab-8443-b5d8b39573db.png)

Vous cliquez sur le boutton `Generate Fleet Server Policy` pour générer la policy vu qu'un Fleet Server est de base un Elastic Agent donc il nécessite une policy.

Ensuite, vous tapez les commandes mentionnées pour commencer l'installation de votre Fleet Server dans votre machine.

⚠️ Pourquoi je dois installer un Elastic Agent ? 🤔  ⚠️ <br>
=> Fleet Server est lui-même un type spécial d'elastic agent dédié à la gestion de le flotille d'agents Elastic


![image (5)](https://user-images.githubusercontent.com/123748177/235185830-6e642b02-2d9b-4369-a662-a2dbcac9fd9a.png)


```
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.7.0-linux-x86_64.tar.gz
```
```
tar xzvf elastic-agent-8.7.0-linux-x86_64.tar.gz
```
```
cd elastic-agent-8.7.0-linux-x86_64
```
```
sudo ./elastic-agent install \
  --fleet-server-es=https://172.30.2.238:9200 \
  --fleet-server-service-token=AAEAAWVsYXN0aWMvZmxlZXQtc2VydmVyL______EXEMPLE_DE_TOKEN____jU6emcyNVlnU1FQQ \
  --fleet-server-policy=fleet-server-policy \
  --fleet-server-es-ca-trusted-fingerprint=3da69d2bc85592577cffcd27c08c5fd716d447b47c2e5b4f7dbd712c19d14784
```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/a8f11b7b-b238-4286-9187-a6ed93fb3dc5)

Lorsque vous terminez l'installation, vous aurez une confirmation que votre Fleet Server est bien connecté: 

![image (6)](https://user-images.githubusercontent.com/123748177/235185826-6207b2e0-d961-4be6-ba4a-bb8c57b84c4d.png)

Dans la partie `Fleet` > `Agents`, vous pouvez visualiser le Fleet server que vous venez d'ajouter.

![image (7)](https://user-images.githubusercontent.com/123748177/235185818-e0c637c1-af60-4fdf-8c61-498afb99aa0c.png)

Et dans la partie `Fleet` > `Agent Policies`, vous pouvez visualiser la Fleet server policy créée

![image (8)](https://user-images.githubusercontent.com/123748177/235185814-dce93a77-9637-4900-ab73-203f1e8f4616.png)

