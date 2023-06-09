## Création d'une nouvelle intégration

Dans cette partie, vous allez créer une intégration de type `Custom logs`:

Pour ajouter cette intégration, redirigez vous vers l'interface `Integrations` dans le menu `Management`.

![image](https://user-images.githubusercontent.com/123748177/234854155-b648f4d5-c6a3-4373-ba68-5dc42cb26d0c.png)

Vous pouvez également accéder aux intégrations depuis la policy de l'agent auquel vous souhaitez rajouter l'intégration

![policy](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/28ea8ab9-c6ea-434f-811e-c9c896fa4763)


![Add Integration](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/e05d4cbc-787a-4677-ae89-7050ac0ab6e6)


Puis, vous cherchez `Custom Logs`: 

![custom logs](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/4201dd6f-1a1a-4b28-925b-347181284106)

Maintenant, cliquez `Add Custom Logs`

![add custom log](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/f64f4961-742c-49fb-994a-4310c002c1de)

- Vous donnez d'abord un nom de votre instance d'intégration :

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/9468fd4b-0f82-4200-acb6-38e870305541)

- Ensuite, vous allez mentionné le chemin vers le fichier qui contient vos logs. Dans votre cas, vous allez créez un fichier log de test, dans lequel vous allez ajouté des logs provisoires, dans la machine qui contient votre `Elastic Agent`. 

Pour ce faire, créez le fichier suivant dans le répertoire `/var/log`:

 ```
 vi test.log
 ```
 - Puis, copiez le chemin vers le fichier dans le champ `Log file path`: 

![configure integration 1](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/e402153f-3d69-40fd-91ce-31004ff4132f)

- Dans la partie `Advanced options`, ajoutez le nom de votre dataset: 

![configure log file](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/fe8a592b-acfc-4092-882a-2a361b131d06)

- Finalement, vous précisez la policy auquel l'intégration va etre ajoutée. 

![policy](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/d63c43bd-862f-4999-b941-54a99ec8e5f1)



- Pour vérifiez que tout marche bien, créez un `Data View` pour pouvoir visualiser les logs ingérés. 
- Dans un premier temps, vous devez aller insecpter les Data Streams crées : 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/6c37bb49-de7e-4b2e-9da2-e7ea87f1d542)

![Screenshot 2023-05-13 at 13 34 09](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/8b1d88f3-3e77-499f-ab45-cf9ea1ed1b63)

- Pour pouvoir visualiser ce Data Stream vous devez avoir des données dans votre fichier de logs. Insérez une ligne de log dans le fichier `test.log`

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/c52c957c-7ff6-4641-8e97-59eff456deac)

- Allez ensuite dans le menu `Data Views` dans le menu `Stack Management` puis cliquez `Create data view`. 

- Entrez un wilcard de manière a faire pointer votre data view vers le data stream nouvellement créé

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/c54e621c-767b-48bf-a2c0-36ef5bad1dc6)



- Maintenant, redirigez vous vers `Analytics` > `Discover`. En haut à gauche, choissisez le Data view que vous venez de créer. Vous aurez un résultat comme le suivant: 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/5cd4b594-1173-4c8f-b551-7262629daed3)

Si vous développez le document inséré, vous pouvez bien voir qu'il contient le message de log que vous avez saisi (tout en bas):

![Screenshot 2023-05-13 at 13 25 21](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/99c53a63-b831-4496-8a2e-44054e50c5dc)

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/590ff8e2-dcfa-44e8-975c-42016c999082)


Vous pouvez épingler les champs de votre choix dans la vue colonne :

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/e0ccd701-7630-4f22-90f2-6adc9aea00d8)

Et également dans la vue détaillée :

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/ee938531-de1c-41ee-9e31-bccaa570e4f7)

La vision filtrée sur votre message est ainsi plus claire : 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/6350291d-fab4-436f-8588-75d6fd20dcb6)








