## <a name="deploy-uploaded-zip-file"></a>Развертывание загруженного ZIP-файла

В Cloud Shell разверните загруженный ZIP-файл в веб-приложение с помощью команды [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Обязательно замените значение *\<имя_приложения>* именем вашего веб-приложения.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

Эта команда позволяет развертывать файлы и каталоги из ZIP-файлов в папку для приложения службы приложений по умолчанию (`\home\site\wwwroot`). Затем приложение перезапускается. Если настроен любой дополнительный пользовательский процесс сборки, он также запустится.
