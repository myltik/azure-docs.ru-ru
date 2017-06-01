---
title: "Создание первого веб-приложения Python в Azure за пять минут | Документация Майкрософт"
description: "Разверните первое приложение Python Hello World в веб-приложении службы приложений за считаные минуты."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: df34052acc401fb5bb1e3f808c649c0ea0bcf33c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017

---
# <a name="create-a-python-application-on-web-app"></a>Создание приложения Python в веб-приложении

В этом кратком руководстве описано, как разработать и развернуть приложение Python в Azure. Мы запустим приложение, используя службу приложений Azure, а затем создадим и настроим в ней новое веб-приложение с помощью интерфейса командной строки Azure. Затем мы используем Git, чтобы развернуть наше приложение Python в Azure.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux. На все это вам понадобится около 5 минут.

## <a name="prerequisites"></a>Предварительные требования

Перед запуском этого примера необходимо скачать и установить следующие компоненты в локальной среде:

1. [Git.](https://git-scm.com/)
1. [Python.](https://www.python.org/downloads/)
1. [Azure CLI 2.0.](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="download-the-sample"></a>Скачивание примера приложения

Клонируйте репозиторий примера приложения Hello World на локальный компьютер.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

> [!TIP]
> Можно также [скачать пример](https://github.com/Azure-Samples/Python-docs-hello-world/archive/master.zip) в виде ZIP-файла и извлечь его.

Перейдите в каталог, в котором содержится образец кода.

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>Локальный запуск приложения

Запустите приложение в локальной среде, открыв окно терминала с помощью командной строки `Python`, чтобы запустить встроенный веб-сервер Python.

```bash
python main.py
```

Откройте браузер и перейдите к примеру.

```bash
http://localhost:5000
```

На странице отобразится сообщение **Hello World** из примера приложения.

![localhost-hello-world-in-browser](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

В окне терминала нажмите клавиши **CTRL+C**, чтобы выйти из веб-сервера.

## <a name="log-in-to-azure"></a>Вход в Azure

Теперь используем Azure CLI 2.0 в окне терминала для создания ресурсов, необходимых для размещения приложения Python в Azure. Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

Для использования FTP и локального репозитория Git на сервере необходимо настроить пользователя развертывания, чтобы выполнить проверку подлинности развертывания. Создание пользователя развертывания выполняется однократно. Запишите имя пользователя и пароль, так как они потребуются нам в дальнейшем.

> [!NOTE]
> Пользователь развертывания необходим для развертывания FTP и локального репозитория Git в веб-приложении.
> `username` и `password` являются учетными данными на уровне учетной записи и отличаются от учетных данных подписки Azure. **Эти учетные данные создаются только один раз**.
>

Для их создания используйте команду [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set).

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Создание службы приложений Azure

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

В следующем примере создается план службы приложений с именем `quickStartPlan` и ценовой категорией **Бесплатный**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

После создания плана службы приложений в Azure CLI отображается информация следующего вида.

```json
{
"appServicePlanName": "quickStartPlan",
"geoRegion": "North Europe",
"id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
"kind": "app",
"location": "North Europe",
"maximumNumberOfWorkers": 1,
"name": "quickStartPlan",
"provisioningState": "Succeeded",
"resourceGroup": "myResourceGroup",
"sku": {
  "capacity": 0,
  "family": "F",
  "name": "F1",
  "size": "F1",
  "tier": "Free"
},
"status": "Ready",
"type": "Microsoft.Web/serverfarms",
}
```

## <a name="create-a-web-app"></a>Создание веб-приложения

После создания плана службы приложений `quickStartPlan` создайте веб-приложение в рамках этого плана. Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Выполните команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение.

В приведенной ниже команде замените заполнитель `<app_name>` уникальным именем приложения. `<app_name>` будет использоваться по умолчанию в качестве сайта DNS для веб-приложения. Поэтому это имя должно быть уникальным для всех приложений в Azure. Позже можно сопоставить любые пользовательские записи DNS с веб-приложением, прежде чем предоставлять его пользователям.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

После создания веб-приложения в Azure CLI отображается информация следующего вида.

```json
{
  "clientAffinityEnabled": true,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "hostNames": [
    "<app_name>.azurewebsites.net"
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
  "kind": "app",
  "location": "North Europe",
  "outboundIpAddresses": "13.69.190.80,13.69.191.239,13.69.186.193,13.69.187.34",
  "resourceGroup": "myResourceGroup",
  "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "state": "Running",
  "type": "Microsoft.Web/sites",
}
```

Перейдите на сайт, чтобы просмотреть только что созданное веб-приложение.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-python/app-service-web-service-created.png)

Мы создали пустое веб-приложение в Azure. Теперь настроим в веб-приложении использование языка Python и развернем в нем наше приложение.

## <a name="configure-to-use-python"></a>Настройка использования Python

Используйте команду [az appservice web config update](/cli/azure/app-service/web/config#update), чтобы настроить в веб-приложении использование языка Python версии `3.4`.

> [!TIP]
> При такой настройке версии Python будет использоваться контейнер по умолчанию, предоставляемый платформой. Если вы хотите использовать собственный контейнер, ознакомьтесь с командой [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) в справочнике по интерфейсу командной строки CLI.

```azurecli
az appservice web config update --python-version 3.4 --name <app-name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>Настройка локального развертывания Git

Для развертывания в веб-приложении можно использовать FTP, локальный репозиторий Git, GitHub, Visual Studio Team Services и Bitbucket.

Используйте команду [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git), чтобы настроить доступ локального репозитория Git к веб-приложению.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Скопируйте выходные данные из терминала, так как они понадобятся на следующем этапе.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

Добавьте удаленную службу приложений Azure в локальный репозиторий Git.

```bash
git remote add azure <paste-previous-command-output-here>
```

Выполните публикацию в удаленную службу приложений Azure, чтобы развернуть приложение. После этого введите пароль, указанный ранее в процессе создания пользователя развертывания.

```azurecli
git push azure master
```

Во время развертывания служба приложений Azure будет взаимодействовать с Git.

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Переход в приложение

Перейдите в развертываемое приложение с помощью веб-браузера.

```bash
http://<app_name>.azurewebsites.net
```

Теперь страница, на которой отображается сообщение Hello World, выполняется с использованием нашего кода Python как веб-приложение службы приложений Azure.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

## <a name="updating-and-deploying-the-code"></a>Обновление и развертывание кода

В локальном текстовом редакторе в приложении Python откройте файл `main.py` и внесите небольшое изменение в текстовой строке рядом с инструкцией `return`:

```python
return 'Hello, Azure!'
```

Зафиксируйте изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git commit -am "updated output"
git push azure master
```

После завершения развертывания переключитесь в окно браузера, открытое на этапе перехода в приложение, и нажмите кнопку "Обновить".

![hello-azure-in-browser](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Управление новым веб-приложением Azure

Перейдите на портал Azure, чтобы просмотреть созданное приложение.

Для этого войдите на портал [https://portal.azure.com](https://portal.azure.com).

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-get-started-python/app-service-list.png)

Вы попадете в _колонку_ веб-приложения (страница портала, открывшаяся горизонтально).

По умолчанию колонка веб-приложения отображает страницу **обзора**. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. Вкладки в левой области колонки отображают разные страницы конфигурации, которые вы можете открыть.

![Колонка службы приложений на портале Azure](media/app-service-web-get-started-python/app-service-detail.png)

На вкладках в колонке содержится множество полезных функций, которые вы можете добавить в веб-приложение. Ниже представлены лишь некоторые из возможностей:

* сопоставление настраиваемого DNS-имени;
* привязка настраиваемого SSL-сертификата;
* настройка непрерывного развертывания;
* вертикальное и горизонтальное масштабирование;
* добавление аутентификации пользователей.

**Поздравляем!** Вы развернули свое первое приложение Python в службе приложений.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры Azure CLI](app-service-cli-samples.md)

