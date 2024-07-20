# Azure AZ cli commands for handy usage

### Reference:
 - https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli-service-principal
 - https://learn.microsoft.com/en-us/cli/azure/cheat-sheet-onboarding

### Az login with service principle
```bash
az login --service-principal -u <app-id> -p <password-or-cert> --tenant <tenant>

Eg: az login --service-principal -u 20c600d7-9a94-4ebb-98b8-xxxxxxx -p Bnh8Q~nvPP1VZ9bwomSO5vpTlwdYW5jxxxxxx --tenant 84f1e4ea-8554-43e1-8709-f0xxxxxxx
```

### To avoid displaying your password on console and are using az login interactively, use the read -s command under bash.
```bash
read -sp "Azure password: " AZ_PASS && echo && az login --service-principal -u <app-id> -p $AZ_PASS --tenant <tenant>
```

### Under PowerShell, use the Get-Credential cmdlet.
```bash
$AzCred = Get-Credential -UserName <app-id>
az login --service-principal -u $AzCred.UserName -p $AzCred.GetNetworkCredential().Password --tenant <tenant>
```
