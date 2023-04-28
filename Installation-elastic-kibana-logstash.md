## Installation d'Elasticsearch et Kibana

Ce workshop a pour objectif de fournir un guide pratique pour l'installation d'Elasticsearch et Kibana sur une machine single, tout en se concentrant sur les aspects fondamentaux de la configuration et de l'utilisation de ces deux outils.  

Les étapes fournies dans ce workshop vous permettront de mettre en place rapidement une instance d'Elasticsearch et de Kibana fonctionnelle, que vous pourrez utiliser pour explorer et analyser vos données.

## 1- Installation Elasticsearch:

- Tout d'abord, accédez à votre machine pour commencer l'installation.
- Lorsque vous êtes bien connectés à votre machine, vous basculez en mode administrateur, et vous vous déplacez vers le répertoire racine, en tapant les commandes suivantes : 
  
 ``` 
 sudo su
 cd
 ```
- Installez Elasticsearch en tapant les commandes suivantes: 
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
```
```
sudo apt-get install apt-transport-https
```
```
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
```
```
sudo apt-get update && sudo apt-get install elasticsearch
```

- Vous devez apporter des modifications au fichier principal de configuration à l'aide de la commande suivante: 
```
vi /etc/elasticsearch/elasticsearch.yml
```
- Ajoutez les modifications suivantes:

```
network.host: 127.0.0.1

http.port: 9200

```
tips : 
```
sed -i 's/#network.host: 192.168.0.1/network.host: 127.0.0.1/g' /etc/elasticsearch/elasticsearch.yml
```

- Lancez Elasticsearch:
```
systemctl daemon-reload

systemctl enable elasticsearch

systemctl start elasticsearch
```

- Puis, on vérifie l'état du cluster pour vérifier que tout marche correctement.

```
systemctl status elasticsearch
```
![image](https://user-images.githubusercontent.com/123748177/227985805-e2755adf-9942-4f90-8f14-c8b6bfa7ec5b.png)

-Maintenant, vous allez pinguer l'API elastic pour vérifier l'état du noeud:
```
curl http://127.0.0.1:9200/
```

## 2- Installation Kibana:

Vous installez Kibana à l'aide de la commande suivante:
```
sudo apt-get update && sudo apt-get install kibana
```
Vous accédez par la suite au fichier de configuration pour modifier les parties nécessaires:
```
vi /etc/kibana/kibana.yml
```
Puis, vous apportez les modifications suivantes: 
```
server.port: 5601

server.host: "0.0.0.0"

elasticsearch.hosts: ["http://localhost:9200"]
```
**Il ne vous reste qu'à démarrer Kibana :)**
Pour ce faire, exécuter les commandes suivantes: 
```
systemctl daemon-reload

systemctl enable kibana

systemctl start kibana
```
- Puis, on vérifie l'état du Kibana pour vérifier que tout marche correctement.

```
systemctl status kibana
```
![image](https://user-images.githubusercontent.com/123748177/228309327-a8321452-8bd4-4621-83cd-160b9c670f13.png)

**Félicitations!! Vous pouvez maintenant vous connecter à Kibana à partir de votre navigateur en utilisant l'adresse IP Public de votre machine suivi par le port 5601**

## 3- Installation Logstash:

Vous installez Logstash à l'aide de la commande suivante:
```
sudo apt-get update && sudo apt-get install -y logstash
```

Pour tester que Logstash s'exécute correctement, vous allez créer une simple pipeline de test.

Dans le répertoire `etc/logstash`, créez les deux fichiers suivants : 

- `hello.txt` dans lequel vous tapez `hello logstash` 

- `easy-pipeline.conf` dans lequel vous tapez la configuration suivante:

```
input {
  file {
    path => "/etc/logstash/hello.txt"
    start_position => "beginning"
  }
}
output {
  elasticsearch {
    hosts => ["http://IP-ADRESS:9200"]
    index => "hello-logstash-%{+YYYY.MM.dd}"
    user => "elastic"
    password => "elastic"
    ssl => true
    cacert => "/etc/logstash/config/certs/http_ca.crt"
  }
}
```

Vous copiez le fichier de certificat d'Elasticsearch dans le répertoire des certificats de Logstash: 

```
cp /etc/elasticsearch/certs/http_ca.crt  /etc/logstash/config/certs/http_ca.crt 
```

Vous tapez la commande suivante pour exécuter la pipeline de test: 

```
/usr/share/logstash/bin/logstash -f /etc/logstash/easy-pipeline.conf
```

**Vérifiez qu'un index qui porte le nom `hello-logstash` est bien créé**