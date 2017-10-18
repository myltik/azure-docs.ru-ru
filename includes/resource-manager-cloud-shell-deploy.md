## <a name="deploy-template-from-cloud-shell"></a>Развертывание шаблона из Cloud Shell

Для развертывания шаблона можно использовать [Cloud Shell](../articles/cloud-shell/overview.md). Однако сначала необходимо загрузить шаблон в общий файловый ресурс для Cloud Shell. Если вы еще не использовали Cloud Shell, ознакомьтесь со статьей [Обзор Azure Cloud Shell (предварительная версия)](../articles/cloud-shell/overview.md), чтобы узнать о настройке службы.

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите свою группу ресурсов Cloud Shell. Шаблон имени — `cloud-shell-storage-<region>`.

   ![Выбор группы ресурсов](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Выберите учетную запись хранения для Cloud Shell.

   ![Выбор учетной записи хранения](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Выберите **Файлы**.

   ![Выбор файлов](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Выберите общий файловый ресурс для Cloud Shell. Шаблон имени — `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Выбор общего файлового ресурса](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. Выберите **Добавить каталог**.

   ![Добавление каталога](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. Назовите его **templates** и нажмите кнопку **ОК**.

   ![Присвоение имени каталогу](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. Выберите новый каталог.

   ![Выбор каталога](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. Щелкните **Отправить**.

   ![Кнопка "Отправить"](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. Найдите и отправьте свой шаблон.

   ![Отправка файла](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. Откройте командную строку.

   ![Открытие Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
