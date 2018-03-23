## <a name="rest"></a>Развертывание ZIP-файла с помощью с REST API 

Вы можете использовать [REST API службы развертывания](https://github.com/projectkudu/kudu/wiki/REST-API), чтобы развернуть ZIP-файл в приложении в Azure. Для развертывания отправьте запрос POST по адресу https://<имя_приложения>.scm.azurewebsites.net/api/zipdeploy. В тексте сообщения запроса POST должен содержаться ZIP-файл. Учетные данные развертывания для приложения указываются в запросе с использованием обычной проверки подлинности HTTP. Дополнительные сведения см. в [справочнике по принудительному развертыванию из ZIP-файлов](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Для обычной аутентификации HTTP требуются учетные данные развертывания службы приложений. См. дополнительные сведения об [установке и сбросе учетных данных на уровне пользователя](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Использование cURL

В примере ниже для развертывания ZIP-файла используется средство cURL. Замените заполнители `<username>`, `<password>`, `<zip_file_path>` и `<app_name>`. Когда в cURL появится запрос, введите пароль.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Этот запрос позволяет активировать принудительное развертывание из отправленного ZIP-файла. Вы можете просмотреть текущие и предыдущие развертывания, используя конечную точку https://<имя_приложения>.scm.azurewebsites.net/api/deployments, как показано в примере cURL ниже. Точно так же замените `<app_name>` именем приложения и `<deployment_user>` — именем пользователя из учетных данных развертывания.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>С помощью PowerShell

В примере ниже для отправки запроса, содержащего ZIP-файл, используется [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod). Замените заполнители `<deployment_user>`, `<deployment_password>`, `<zip_file_path>` и `<app_name>`.

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

Этот запрос позволяет активировать принудительное развертывание из отправленного ZIP-файла. Чтобы просмотреть текущие и прошлые развертывания, выполните следующие команды. Замените заполнитель `<app_name>`.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
