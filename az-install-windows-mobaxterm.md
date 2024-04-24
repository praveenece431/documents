# Install AZ cli in both windows and mobaxterm

### Execute the below command in powershell
[Azure cli installation](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=powershell)
```bash
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'; Remove-Item .\AzureCLI.msi
```
