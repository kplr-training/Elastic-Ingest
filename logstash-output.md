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


5- Maintenant, vous devez créer votre autorité de certificat pour avoir votre certificat et sa clé privée.

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


7- Vous copiez le contenu de `ca.crt` dans la partie `Client SSL Certificate` et le contenu de `ca.key` dans la partie `Client SSL Certificate key`

![image](https://user-images.githubusercontent.com/123748177/235174854-2deeec0a-d29e-4ce1-a2c9-db3aa616089e.png)

9- Ajoutez la partie de code suivante dans le fichier de configuration de Kibana `kibana.yml`

```
xpack.encryptedSavedObjects:
  encryptionKey: "min-32-byte-long-strong-encryption-key"
```

10- Cliquez `Save and Apply settings`, et Voilà! vous avez créé votre output avec succès.

![image](https://user-images.githubusercontent.com/123748177/235177522-107ad5a2-2c35-4404-aa3f-44d2a6c3744f.png)
