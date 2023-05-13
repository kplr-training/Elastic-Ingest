
# Creation d'un Elastic Agent Ubuntu

- Aller dans Fleet et ajouter un agent

![Fleet](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/617fcd52-bc2b-454c-9155-82c702a9a860)

- avant tout, on crée d'abord une policy pour l'agent. 
l'agent policy va syndiquer toutes les intégrations configurées à l'agent auquel elle est rattachée. 


![Policy](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/fa4e8938-106e-4594-9201-45490e7e14b1)

- Entrez ensuite les commandes qui sont affichées dans l'interface :<br>
⚠️ IMPORTANT : Il est important de rajouter le flag `--insecure`<br>
autrement vous aurez le message d'erreur `x509: certificate signed by unknown authority` <br>
en effet, l'autorité de certification est inconnue par l'agent et l'on doit la déclarer car on utilise un certificat self-signed. <br>
Pour faciliter les choses en contexte de dev/test, on passe outre cette vérification.<br>
Il est toutefois important de noter que le traffic est effectivement crypté, juste que l'autorité de certification n'est pas validée. <br>
(_Autrement dit l'agent n'as pas la certitude que le fleet est bien "celui qu'il prétend être"_)<br>

```
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.7.1-linux-x86_64.tar.gz
tar xzvf elastic-agent-8.7.1-linux-x86_64.tar.gz
cd elastic-agent-8.7.1-linux-x86_64
sudo ./elastic-agent install --url=https://44.208.194.116:8220 --enrollment-token=UHhhd0ZJZ0Jvd09obHRCaVN4bVA6cVppYlA1ZUVTTWVOaTR5QS12eXhjQQ== --insecure
```

On confirme que l'agent est bien enrollé dans le Fleet Server et que l'on reçoit bien du flux : 

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/e00b50de-5439-458c-8606-1f04fea229f4

![image](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/1cbbe8ff-041a-4735-b32d-3de50c504c69)


A présent il ne reste plus qu'à créer des intégrations et les ajouter à l'agent policy fraîchement créée

