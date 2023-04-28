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
    ssl_certificate_authorities => ["<ca_path>"]
    ssl_certificate => "<server_cert_path>"
    ssl_key => "<server_cert_key_in_pkcs8>"
    ssl_verify_mode => "force_peer"
  }
}

output {
  elasticsearch {
    hosts => "<es_host>"
    api_key => "<api_key>"
    data_stream => true
    ssl => true
    # cacert => "<elasticsearch_ca_path>"
  }
}

````

3- Dans votre répertoire de configuration Logstash, ouvrez le fichier `pipelines.yml` et ajoutez la configuration suivante.

NB: Remplacez le chemin vers votre fichier.

```
- pipeline.id: elastic-agent-pipeline
  path.config: "/etc/path/to/elastic-agent-pipeline.conf"

```

4- Maintenant, vous devez créer votre autorité de certificat pour avoir votre certificat et sa clé privée.

Pour ce faire, vous devez exécutez la commande suivante: 
```
./bin/elasticsearch-certutil ca --pem
```
Cette commande crée un fichier zip contenant le certificat CA et la clé que vous utiliserez pour signer les certificats. 

5-  Extrayez le fichier zip :

```
unzip 
```