## Création et configuration d'un Output de type Logstash 

Un agent output de type Logstash fait référence à une instance de Logstash configurée pour collecter, traiter et envoyer des données vers une destination spécifique. 

Dans ce contexte, l'agent output de type Logstash est utilisé pour acheminer les données des agents vers Logstash pour etre collectées et traitées. 

**Création d'un Output Logstash** 

Pour créer un nouveau output, redirigez vous vers `Management` > `Fleet` > `Settings` > `Outputs` > `Add Output`

![Fleet Add OUtput](https://user-images.githubusercontent.com/123748177/235136317-474b64aa-2111-4e2c-87ac-20cf69427eea.png)

Vous saissisez un nom d'output et puis vous choisissez comme type : ` Logstash `

![output](https://user-images.githubusercontent.com/123748177/235136771-1d58e3d5-9f9a-4fd4-a137-5b253623465f.png)

Ensuite, vous devez suivre les étapes de configuration additionnelle dans Logstash pour compléter la création de l'output: 

![image](https://user-images.githubusercontent.com/123748177/235137339-5e837a48-c540-4de7-bee4-d5d270a56a98.png)

1- Vous générez d'abord l'API key.

2- Ouvrez la fichier `elastic-agent-pipeline.conf` et ajoutez la configuration suivante (REMARQUE! : Vous allez la modifier par la suite):

````
input {
  elastic_agent {
    port => 5044
    ssl => true
    #ssl_certificate_authorities => ["<ca_path>"]
    ssl_certificate => "/etc/logstash/config/certs/ca/ca.crt"
    ssl_key => "/etc/logstash/config/certs/ca/ca.key"
    ssl_verify_mode => "none"
  }
}
output {
  elasticsearch {
    hosts => "https://ELASTIC-IP-ADRESS:9200"
    api_key => "GENERATED API KEY"
    data_stream => true
    ssl => true
    cacert => "/etc/logstash/config/certs/http_ca.crt"
  }
}

````

3- Dans votre répertoire de configuration Logstash, ouvrez le fichier `pipelines.yml` et ajoutez la configuration suivante.

NB: Remplacez le chemin vers votre fichier.

```
- pipeline.id: elastic-agent-pipeline
  path.config: "/etc/path/to/elastic-agent-pipeline.conf"

```

4- Vous devez précisez l'adresse que les agents vont utiliser pour se connecter à Logstash

![image](https://user-images.githubusercontent.com/123748177/235174010-7e450e63-7c76-482f-9880-0cf9a6a527fc.png)


5- Maintenant, vous devez créer votre autorité de certificat.

Pour ce faire, vous devez exécutez la commande suivante: 
```
/usr/share/elasticsearch/bin/elasticsearch-certutil ca --pem
```
Cette commande crée un fichier zip contenant le certificat CA et la clé que vous utiliserez pour signer les certificats. 

6-  Extrayez le fichier zip :

```
unzip elastic-stack-ca.zip
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


7- Vous copiez le contenu de `ca.crt` dans la partie `Server SSL certificate authorities` 

![image](https://user-images.githubusercontent.com/123748177/235361006-925922d7-064f-4543-8287-48a284484dfd.png)

8- Vous créez maintenant le certificat Client à l'aide de la commande suivante : 

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


9- Vous créez aussi le certificat Logstash Server : 

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

![image](https://user-images.githubusercontent.com/123748177/235362724-63f8a7f1-c89a-4077-bef1-8012690d711c.png)

10- Convertissez la clé Logstash vers pkcs8.

```
openssl pkcs8 -inform PEM -in logstash.key -topk8 -nocrypt -outform PEM -out logstash.pkcs8.key
```

11- Vous copiez le contenu de `client.crt` dans la partie `Client SSL certificate` et le contenu de `client.key` dans la partie `Client SSL certificate key`


![image](https://user-images.githubusercontent.com/123748177/235361083-74685b14-09da-48ef-94bd-1c4e303dbfe9.png)


11- Ajoutez la partie de code suivante dans le fichier de configuration de Kibana `kibana.yml`

```
xpack.encryptedSavedObjects:
  encryptionKey: "min-32-byte-long-strong-encryption-key"
```

12- Modifiez le fichier `elastic-agent-pipeline.conf` en remplissant les données manquantes : 

```
input {
  elastic_agent {
    port => 5044
    ssl => true
    ssl_certificate_authorities => ["/path/to/ca.crt"]
    ssl_certificate => "/path/to/logstash.crt"
    ssl_key => "/path/to/logstash.pkcs8.key"
    ssl_verify_mode => "force_peer"
  }
}

output {
  elasticsearch {
    hosts => "https://xxxx:9200"
    api_key => "xxxx:xxxx"
    data_stream => true
    ssl => true
    cacert => "/path/to/http_ca.crt" 
  }
}
```

13- Cliquez `Save and Apply settings`, et Voilà! vous avez créé votre output avec succès.

![image](https://user-images.githubusercontent.com/123748177/235177522-107ad5a2-2c35-4404-aa3f-44d2a6c3744f.png)
