## Redirection Conditionnelle d'Elasticsearch output vers différents index

Si vous envoyez des événements vers le même cluster Elasticsearch, mais que vous ciblez des index différents, vous pouvez: 

- Utiliser différentes sorties Elasticsearch, chacune avec une valeur différente pour le paramètre d'index.
- Utiliser une seule sortie Elasticsearch et utiliser la substitution de variable dynamique pour le paramètre d'index.

Afin de minimiser le nombre de connexions ouvertes vers Elasticsearch, il est généralement plus efficace d'avoir une seule sortie Elasticsearch. 

**Donc, l'objectif de ce workshop est de créer une pipeline logstash qui va utiliser une seule sortie Elasticsearch mais les données seront redirigés vers différents indices selon la valeur de votre message.**

### Création de la pipeline

1- Dans votre machine, créez un nouveau fichier de configuration d'un pipeline Logstash.

2- Configurer votre entrée qui sera un Elastic Agent.

3- Ajoutez des processeurs qui permet de conditionner la création d'un champ [@metadata][target_index] en fonction de la valeur du champ message. 

- Si la valeur de message est "test" ou "staging", le champ [@metadata][target_index] sera défini avec le format "test-YYYY.MM" où YYYY représente l'année et MM le mois courant. 

- Si la valeur de message est "production", le champ [@metadata][target_index] sera défini avec le format "prod-YYYY.MM.dd" où YYYY représente l'année, MM le mois courant et dd le jour courant. 

- Pour toute autre valeur de message, le champ [@metadata][target_index] sera défini avec le format "unknown-YYYY" où YYYY représente l'année courante. 

-Ce champ [@metadata][target_index] peut ensuite être utilisé dans la sortie Elasticsearch pour spécifier l'index de destination des événements traités par le filtre.

```
filter {
   if [message] in [ "test", "staging" ] {
        mutate { add_field => { "[@metadata][target_index]" => "test-%{+YYYY.MM}" } }
   } else if [message] == "production" {
        mutate { add_field => { "[@metadata][target_index]" => "prod-%{+YYYY.MM.dd}" } }
   } else {
        mutate { add_field => { "[@metadata][target_index]" => "unknown-%{+YYYY}" } }
   }
}
```

4- Configurez votre output en donnant comme nom de l'index la valeur `[@metadata][target_index]` et mettant la valeur `false` pour le champ data_stream:

```
output {
  elasticsearch {
    hosts => "https://ES-LOCAL-ADRESS-IP:9200"
    user=>"elastic"
    password=>"elastic"
    data_stream=> false
    index => "%{[@metadata][target_index]}"
    ssl => true
    cacert => "/etc/logstash/config/certs/http_ca.crt"
  }
}
```

5- Lancez votre pipeline à l'aide de la commande suivante: 

```
/usr/share/logstash/bin/logstash -f /etc/logstash/pipeline-name.conf
```

6- Ajoutez des messages de log dans votre fichier de log de test qui peuvent vérifier les 3 cas de votre processeur.

7- Redirigez vous vers `Stack Management` > `Index Management`.

8- Vérifiez que vous avez bien les différents indices créés qui correspondent aux messages de logs que vous avez ajouté.

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/a8b22afb-1841-44f3-a2d9-9ef679c56134)

