## Création et configuration d'un Output de type Logstash 

Un agent output de type Logstash fait référence à une instance de Logstash configurée pour collecter, traiter et envoyer des données vers une destination spécifique. 

Dans ce contexte, l'agent output de type Logstash est utilisé pour acheminer les données des agents vers Logstash pour etre collectées et traitées. 

---

### Création d'un Output Logstash

Pour créer un nouveau output, redirigez vous vers `Management` > `Fleet` > `Settings` > `Outputs` > `Add Output`

![Fleet Add OUtput](https://user-images.githubusercontent.com/123748177/235136317-474b64aa-2111-4e2c-87ac-20cf69427eea.png)

Vous saissisez un nom d'output et puis vous choisissez comme type : ` Logstash `

![output](https://user-images.githubusercontent.com/123748177/235136771-1d58e3d5-9f9a-4fd4-a137-5b253623465f.png)

Ensuite, vous devez suivre les étapes de configuration additionnelle dans Logstash pour compléter la création de l'output: 

![image](https://user-images.githubusercontent.com/123748177/235137339-5e837a48-c540-4de7-bee4-d5d270a56a98.png)

### 1- Générez d'abord l'API key.

---

### 2- Dans votre répertoire de configuration Logstash, ouvrez le fichier `pipelines.yml` et ajoutez la configuration suivante.

NB: Remplacez le chemin vers votre fichier.

```
- pipeline.id: elastic-agent-pipeline
  path.config: "/etc/path/to/elastic-agent-pipeline.conf"

```
---

### 3- Vous devez préciser l'adresse que les agents vont utiliser pour se connecter à Logstash.<br> 
   :warning: Spécifiez votre **adresse IP privée** et le **port 5044** :warning:

![image](https://user-images.githubusercontent.com/123748177/235174010-7e450e63-7c76-482f-9880-0cf9a6a527fc.png)

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/2ac736b6-3a72-4a61-91ca-d6111e3100e4)

### 4- Maintenant, vous devez créer votre autorité de certificat que vous utiliserez pour signer les certificats.

Pour ce faire, vous devez exécutez la commande suivante: 
```
/usr/share/elasticsearch/bin/elasticsearch-certutil ca --pem --out /etc/logstash/config/certs/ca.zip
```
Cette commande crée un fichier zip contenant le certificat CA et la clé. 

---

### 5-  Extrayez le fichier zip :

```
apt-get install unzip
unzip /etc/logstash/config/certs/ca.zip
```
![image](https://user-images.githubusercontent.com/123748177/235146035-6fa22863-c645-42b1-a12f-5653dea6e90e.png)

**NB: à ce moment, vous avez créé l'autorité de certification pour signer vos prochains certificats SSL**

Maintenant, vous aurez besoin de créer un certificat de Logstash Server et un certificat de Client.

Le `certificat du serveur Logstash` est utilisé pour sécuriser la connexion entre Logstash et les clients qui s'y connectent.
Ce certificat est utilisé pour vérifier l'identité du serveur Logstash auprès de ses clients et pour chiffrer les données transmises entre eux.

D'autre part, le `certificat client` est utilisé par le serveur Fleet pour s'authentifier auprès de Logstash. 
Ce certificat est généré spécifiquement pour le serveur Fleet et sert à prouver que le serveur Fleet est autorisé à se connecter à Logstash. 
Le certificat client est également utilisé pour chiffrer les données transmises entre le serveur Fleet et Logstash.


![SSl certificats drawio](https://user-images.githubusercontent.com/123748177/235360276-4d67a37c-ae94-4d99-9a5f-78e8bd9ab6c5.png)

---

### 6- Vous copiez le contenu de `ca.crt` dans la partie `Server SSL certificate authorities` 

![image](https://user-images.githubusercontent.com/123748177/235361006-925922d7-064f-4543-8287-48a284484dfd.png)

---

### 7- Vous créez maintenant le certificat Client à l'aide de la commande suivante : 

```
/usr/share/elasticsearch/bin/elasticsearch-certutil cert \
  --name client \
  --ca-cert /etc/elasticsearch/certs/ca/ca.crt \
  --ca-key /etc/elasticsearch/certs/ca/ca.key \
  --pem
```
Extrayez le fichier zip :

```
unzip client.zip
```
![image](https://user-images.githubusercontent.com/123748177/235362711-1cbebe38-6c93-4862-8ebe-56c67f39f93f.png)

---

### 8- Vous créez aussi le certificat Logstash Server : 

```
/usr/share/elasticsearch/bin/elasticsearch-certutil cert \
  --name logstash \
  --ca-cert /etc/elasticsearch/certs/ca/ca.crt \
  --ca-key /etc/elasticsearch/certs/ca/ca.key \
  --pem
```

Extrayez le fichier zip :

```
unzip logstash.zip
```

En somme, vous avez créé 3 archives, contenant respectivement : 
- la paire clé/certificat de l'autorité de certification (ca)
- la paire clé/certificat de logstash
- la paire clé/certificat de fleet (client)
puis vous avez extrait les archives en question. 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/0db1244a-efa3-4c1d-9385-baa4aafee805)

![image](https://user-images.githubusercontent.com/123748177/235362724-63f8a7f1-c89a-4077-bef1-8012690d711c.png)

---

### 9- Convertissez la clé Logstash vers pkcs8.

```
openssl pkcs8 -inform PEM -in logstash.key -topk8 -nocrypt -outform PEM -out logstash.pkcs8.key
```

---

### 10- Vous copiez le contenu de `client.crt` dans la partie `Client SSL certificate` et le contenu de `client.key` dans la partie `Client SSL certificate key`


![image](https://user-images.githubusercontent.com/123748177/235361083-74685b14-09da-48ef-94bd-1c4e303dbfe9.png)

11- Créez le fichier `vi /etc/logstash/elastic-agent-pipeline.conf` et ajoutez la configuration suivante <br>
⚠️ 1 - vous devez faire super attention à ce que les chemins des certificats soient corrects<br>
⚠️ 2 - les chemins des certificats doivent être accessibles  pour éviter les soucis (chmod au besoin)<br>
⚠️ 3 - renseignez correctement l'adresse IP privée du host suivie du port 9200 et la clé API générée précédemment<br>


````
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
  elasticsearch {
    hosts => "https://*****__VOTRE___ADRESSE_IP_PRIVEE__*******:9200"
    api_key => "*****__VOTRE_API_KEY*******"
    data_stream => true
    ssl => true
    cacert => "/etc/logstash/config/certs/http_ca.crt"
  }
}

````

---

### 11- Renseigner une clé d'encription Kibana (étape nécessaire pour le bon fonctionnement de la chaîne)

- Commencez par générer une clé d'encryption Kibana : 
```
/usr/share/kibana/bin/kibana-encryption-keys generate
```
Cela donnera l'ouput suivant : 
```
Settings:
xpack.encryptedSavedObjects.encryptionKey: 1aba07240f83231ecf23d9a52de867f2
xpack.reporting.encryptionKey: 3f54c2f69c2b9e52a120a1cd4c1aab15
xpack.security.encryptionKey: 0f20abe0bf4377f3ff81b7763d7e7a40
```
Ajoutez la partie de code suivante dans le fichier de configuration de Kibana `kibana.yml`
```
xpack.encryptedSavedObjects:
  encryptionKey: "1aba07240f83231ecf23d9a52de867f2"
```

---

### 12- Cliquez `Save and Apply settings`, et Voilà! vous avez créé votre output avec succès.

![image](https://user-images.githubusercontent.com/123748177/235177522-107ad5a2-2c35-4404-aa3f-44d2a6c3744f.png)

---

### 13- Il faut à présent démarrer le service logstash ou directement lancer la pipeline de façon individuelle
