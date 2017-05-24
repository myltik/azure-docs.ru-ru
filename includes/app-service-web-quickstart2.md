Используйте команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение. В следующей команде замените заполнитель `<app_name>` уникальным именем приложения. По умолчанию в качестве сайта DNS для веб-приложения используется `<app_name>`. Если `<app_name>` не является уникальным, вы получите понятное сообщение об ошибке "Веб-сайт с указанным именем <имя_приложения> уже существует".

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

После создания веб-приложения в Azure CLI отображается информация следующего вида:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

Перейдите на сайт (`http://<app_name>.azurewebsites.net`), чтобы просмотреть созданное веб-приложение.

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>Настройка локального развертывания Git

Служба приложений поддерживает несколько способов развертывания содержимого в веб-приложении, например с помощью FTP, локального репозитория Git, GitHub, Visual Studio Team Services и Bitbucket. 

В рамках этого краткого руководства выполним развертывание с помощью локального репозитория Git. Это значит, мы используем команду Git, чтобы отправить код из локального репозитория в репозиторий в Azure. 

Используйте команду [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git), чтобы настроить доступ локального репозитория Git к веб-приложению.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Выходные данные будут иметь следующий формат:

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Сохраните указанный универсальный код ресурса (URI). Он понадобится вам дальше. Параметр `<username>` — это [пользователь развертывания](#configure-a-deployment-user), которого вы создали на предыдущем шаге.

## <a name="push-to-azure-from-git"></a>Перемещение в Azure из Git

Добавьте удаленную службу приложений Azure в локальный репозиторий Git.

```bash
git remote add azure <URI from previous step>
```

Отправьте код в удаленную службу приложений Azure, чтобы развернуть приложение. После этого введите пароль, указанный ранее при создании пользователя развертывания. Убедитесь, что вы ввели пароль, созданный в разделе о [настройке пользователя развертывания](#configure-a-deployment-user), а не тот, что использовался для входа на портал Azure.

```azurecli
git push azure master
```

Предыдущая команда выводит сведения, подобные следующим:

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>Переход в приложение


Перейдите в развернутое приложение:

```
http://<app_name>.azurewebsites.net
```

