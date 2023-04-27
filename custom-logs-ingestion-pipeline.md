## Custom Logs Ingestion Pipeline

Le package personnalisé pour les journaux d'événements Windows vous permet d'ingérer des événements à partir de n'importe quel canal de journal d'événements Windows. 

Vous pouvez obtenir une liste des canaux de journal d'événements disponibles en exécutant la commande suivante :

```
Get-WinEvent -ListLog * | Format-List -Property LogName
```
