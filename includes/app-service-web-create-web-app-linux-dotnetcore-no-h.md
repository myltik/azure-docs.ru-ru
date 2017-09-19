В Cloud Shell создайте [веб-приложение](../articles/app-service/containers/app-service-linux-intro.md) в рамках плана `myAppServicePlan` службы приложений с помощью команды [az webapp create](/cli/azure/webapp#create). Не забудьте указать уникальное имя приложения вместо `<app name>`.

Для среды выполнения в следующей команде задано значение `dotnetcore|1.1`. Чтобы просмотреть все поддерживаемые среды выполнения, выполните команду [az webapp list-runtimes](/cli/azure/webapp#list-runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "dotnetcore|1.1" --deployment-local-git
```

После создания веб-приложения в Azure CLI отобразится примерно следующее:

```json
Local git is configured with url of 'https://<username>@<app name>.scm.azurewebsites.net/<app name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Вы создали пустое веб-приложение в контейнере Linux со включенным развертыванием Git.

> [!NOTE]
> URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<app name>.scm.azurewebsites.net/<app name>.git`. Сохраните этот URL-адрес для дальнейшего использования.
>
