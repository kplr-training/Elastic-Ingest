## Custom Logs Ingestion Pipeline

Le package personnalisé pour les journaux d'événements Windows vous permet d'ingérer des événements à partir de n'importe quel canal de journal d'événements Windows. 

Vous pouvez obtenir une liste des canaux de journal d'événements disponibles en exécutant la commande suivante :

```
Get-WinEvent -ListLog * | Format-List -Property LogName
```
Vous aurez toute la liste des canaux de journal d'événements disponibles affichée de la forme suivante: 

![image](https://user-images.githubusercontent.com/123748177/234847355-b5f3aaa7-2ba5-4401-bdef-79e740381b2b.png)
