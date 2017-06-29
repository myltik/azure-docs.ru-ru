Настройте для веб-приложения локальное развертывание Git с помощью команды [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git).

Служба приложений поддерживает несколько способов развертывания содержимого в веб-приложении, например с помощью FTP, локального репозитория Git, GitHub, Visual Studio Team Services и Bitbucket. В рамках этого краткого руководства выполним развертывание с помощью локального репозитория Git. Это значит, мы используем команду Git, чтобы отправить код из локального репозитория в репозиторий в Azure. 

В следующей команде замените *\<имя_приложения>* именем своего веб-приложения.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Выходные данные будут иметь следующий формат:

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Параметр `<username>` — это [пользователь развертывания](#configure-a-deployment-user), которого вы создали на предыдущем шаге.

Сохраните отображаемый URI. Он понадобится вам позже. 
