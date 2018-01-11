На [портале Azure](https://portal.azure.com) выберите **Группы ресурсов** > **cloud-shell-storage-\<ваш_регион>** > **\<имя_учетной_записи_хранения>**.

![Расположение учетной записи хранения Cloud Shell](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

На странице **Обзор** для учетной записи хранения выберите **Файлы**.

Выберите автоматически созданный файловый ресурс и нажмите **Отправить**. Этот файловый ресурс подключен к Cloud Shell как `clouddrive`.

![Расположение кнопки "Отправить"](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Щелкните селектор файлов, выберите ZIP-файл и нажмите кнопку **Отправить**. 

В Cloud Shell используйте `ls`, чтобы убедиться, что загруженный ZIP-файл отображается в ресурсе по умолчанию `clouddrive`.

```azurecli-interactive
ls clouddrive
```
