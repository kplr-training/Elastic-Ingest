## Création d'un Elastic Agent Windows

- Allez dans Fleet et cliquez `Add agent`

![Add Agent](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/149db55b-11d6-4179-80b9-db09aa5e9e74)

- Ajoutez une nouvelle policy `windows-policy`et confirmez sa création

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/88a52364-286f-462e-bcfd-bded8514e577)


- Ensuite, choisissez `Enroll in Fleet`, pour enroller votre Elastic Agent dans le Fleet server pour qu'il reçoit les mises à jour des politiques automatiquement :

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/b4cac6f5-e046-43fb-9683-ebd5cf65117b)

- Maintenant, en utilisant les commandes données, vous allez installer l'agent dans votre machine Windows. 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/e4967a00-6c52-4d1c-8684-c7b2f09bbd6e)

- Pour ce faire, Lancez le PowerShell 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/6fd7716f-e7c9-4b06-895f-5fef6d722e9a)

- et tapez les commandes suivantes: 

**NB: N'oubliez pas d'ajouter l'option `--insecure` dans la dernière requete d'installation**

```
$ProgressPreference = 'SilentlyContinue'

Invoke-WebRequest -Uri https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.7.1-windows-x86_64.zip -OutFile elastic-agent-8.7.1-windows-x86_64.zip

Expand-Archive .\elastic-agent-8.7.1-windows-x86_64.zip -DestinationPath .

cd elastic-agent-8.7.1-windows-x86_64

.\elastic-agent.exe install --url=https://44.208.194.116:8220 --enrollment-token=d0ZhcEdvZ0I3Q19NM2pRdm1iamk6d1gzR2VkemdUSFctMHd2VzJ0N2dJUQ== --insecure

```

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/12560f2b-2c94-4775-b91f-11af74570fd4)


![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/3b40b387-79b7-4518-923b-a322bcb1449e)

- Lorsque l'installation se termine avec succès, redirigez vous vers l'interface d'ajout de l'agent et vérifiez que l'enrollement de l'agent et les données entrantes sont confirmés: 


![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/dcc5db88-c2b9-48f0-bbf7-298686087cec)

- Cliquez `close` et vérifiez que vous avez bien l'agent ajouté dans la liste des agents :

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123748177/bb16d554-595e-4266-b379-df7cd29e881a)
