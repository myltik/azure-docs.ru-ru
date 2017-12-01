В Cloud Shell создайте [веб-приложение](../articles/app-service/app-service-web-overview.md) в рамках плана `myAppServicePlan` службы приложений с помощью команды [az webapp create](/cli/azure/webapp#create). 

В следующем примере замените *\<app_name>* глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`). 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

После создания веб-приложения в Azure CLI отображаются следующие сведения:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Вы создали пустое веб-приложение с включенным развертыванием Git.

> [!NOTE]
> URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Сохраните этот URL-адрес для дальнейшего использования.
>

Перейдите к только что созданному веб-приложению.

```bash
http://<app_name>.azurewebsites.net
```
