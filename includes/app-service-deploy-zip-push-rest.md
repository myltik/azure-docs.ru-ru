## <a name="rest"></a>Развертывание с помощью API-интерфейсов REST 
 
Можно использовать [API REST службы развертывания](https://github.com/projectkudu/kudu/wiki/REST-API) для развертывания приложения в Azure ZIP-файл. Просто отправьте запрос POST к https://<app_name>.scm.azurewebsites.net/api/zipdeploy. Запрос POST должен содержать ZIP-файл в тексте сообщения. Учетные данные развертывания для приложения предоставляются в запросе с помощью БАЗОВОЙ аутентификации HTTP. Дополнительные сведения см. в разделе [Справочник по развертыванию принудительной .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

В следующем примере средство cURL для отправки запроса, содержащий ZIP-файл. Перелистывание можно запустить его на компьютере Mac или Linux, или с помощью Bash в Windows. Замените `<zip_file_path>` заполнитель путь к расположению ZIP-файла проекта. Кроме того, смените `<app_name>` с уникальным именем приложения.

Замените `<deployment_user>` заполнитель с именем пользователя из учетных данных развертывания. При появлении запроса перелистывание введите пароль. Чтобы узнать, как Настройка учетных данных развертывания для приложения, в разделе [задать и сбросить учетные данные уровня пользователя](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Этот запрос запускает push развертывания из переданного ZIP-файла. Можно просмотреть текущие и прошлые развертываний с помощью https://<app_name>.scm.azurewebsites.net/api/deployments конечной точки, как показано в следующем примере Перелистывание. Опять же, замените `<app_name>` с именем приложения и `<deployment_user>` имя учетных данных развертывания.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```