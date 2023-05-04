## Génération des logs aléatoires

Afin de générer des logs aléatoires pour faire vos tests, créez un fichier `gen-log.ps1` dans lequel vous copiez le code suivant: 

Le code fourni est écrit en PowerShell et il génère des 10 événements de journalisation dans le journal des événements Windows appelé "NetworkSecurity".

```
# Define an array of message fields to use in the event message
$messageFields = @{
    "FailedUser" = @("Alice", "Bob", "Charlie", "David")
    "SuccessUser" = @("Eve", "Frank", "Grace", "Heidi")
    "IPAddress" = @("192.168.0.1", "10.0.0.1", "172.16.0.1", "169.254.0.1")
    "Location" = @("New York", "Los Angeles", "Chicago", "Houston")
}

for($i = 1; $i -le 10; $i++)
{
    $randomEntryType = Get-Random -InputObject @("Error", "Warning", "Information", "SuccessAudit", "FailureAudit")
    $randomMessageField = Get-Random -InputObject $messageFields.Keys
    $randomFieldValue = Get-Random -InputObject $messageFields[$randomMessageField]
    $randomMessage = @{
        "User" = $randomFieldValue
        "IPAddress" = $messageFields.IPAddress[(Get-Random -Minimum 0 -Maximum ($messageFields.IPAddress.Length - 1))]
        "Location" = $messageFields.Location[(Get-Random -Minimum 0 -Maximum ($messageFields.Location.Length - 1))]
        "Description" = @{
            "SuccessAudit" = "User '$($randomFieldValue)' successfully logged in from $($messageFields.IPAddress[(Get-Random -Minimum 0 -Maximum ($messageFields.IPAddress.Length - 1))]) from $($messageFields.Location[(Get-Random -Minimum 0 -Maximum ($messageFields.Location.Length - 1))])."
            "FailureAudit" = "An attempt was made to log in as user '$($randomFieldValue)' from IP address $($messageFields.IPAddress[(Get-Random -Minimum 0 -Maximum ($messageFields.IPAddress.Length - 1))]) from $($messageFields.Location[(Get-Random -Minimum 0 -Maximum ($messageFields.Location.Length - 1))]), but the login was unsuccessful."
        }[$randomEntryType]
    } | ConvertTo-Json -Compress

    $EventId = (Get-Random -Minimum 100 -Maximum 120)
    Write-EventLog -LogName NetworkSecurity -Source NetworkActivity -Message $randomMessage -EventId $EventId -EntryType $randomEntryType
    Start-Sleep -Milliseconds 10
}

```