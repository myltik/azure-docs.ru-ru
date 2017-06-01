---
title: "Создание приложения Node.js в веб-приложении Azure | Документация Майкрософт"
description: "Разверните первое приложение Node.js Hello World в веб-приложении службы приложений за считаные минуты."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/05/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ced6f54603120d8832ee417b02b6673f80a99613
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-nodejs-application-on-web-app"></a>Создание приложения Node.js в веб-приложении

В этом кратком руководстве описано, как разработать и развернуть приложение Node.js в Azure. Мы запустим приложение, используя [план службы приложений Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview), а затем создадим и настроим в нем новое веб-приложение с помощью Azure CLI. Затем мы используем Git, чтобы развернуть наше приложение Node.js в Azure.

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux. На все это вам понадобится около 5 минут.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы скачайте и установите следующие компоненты:

* [Git.](https://git-scm.com/)
* [Node.js и NPM](https://nodejs.org/);
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

Клонируйте репозиторий примера приложения Hello World на локальный компьютер.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Перейдите в каталог, в котором содержится образец кода.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>Локальный запуск приложения

Запустите приложение в локальной среде, открыв окно терминала с помощью скрипта `npm start` для примера приложения, чтобы запустить встроенный HTTP-сервер Node.js.

```bash
npm start
```

Откройте браузер и перейдите к примеру.

```bash
http://localhost:1337
```

На странице отобразится сообщение **Hello World** из примера приложения.

![localhost-hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

В окне терминала нажмите клавиши **CTRL+C**, чтобы выйти из веб-сервера.

## <a name="log-in-to-azure"></a>Вход в Azure

Теперь используем Azure CLI 2.0 в окне терминала, чтобы создать ресурсы, необходимые для размещения приложения Node.js в Azure. Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli
az login
```

<!-- ## Configure a Deployment User -->
[!INCLUDE [login-to-azure](../../includes/configure-deployment-user.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service-plan"></a>Создание плана службы приложений Azure

Создайте [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) уровня "Бесплатный", выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create).

<!--
 An App Service plan represents the collection of physical resources used to ..
-->
[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

В следующем примере создается план службы приложений с именем `quickStartPlan` и ценовой категорией **Бесплатный**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

После создания плана службы приложений в Azure CLI отображается информация следующего вида:

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>Создание веб-приложения

После создания плана службы приложений `quickStartPlan` создайте [веб-приложение](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) в рамках этого плана. Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Используйте команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение.

В приведенной ниже команде замените заполнитель `<app_name>` уникальным именем приложения. По умолчанию в качестве сайта DNS для веб-приложения используется `<app_name>`. Если `<app_name>` не является уникальным, вы получите понятное сообщение об ошибке "Веб-сайт с указанным именем <имя_приложения> уже существует".

<!-- removed per https://github.com/Microsoft/azure-docs-pr/issues/11878
You can later map any custom DNS entry to the web app before you expose it to your users.
-->

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

После создания веб-приложения в Azure CLI отображается информация следующего вида.

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
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

![app-service-web-service-created](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

Мы создали пустое веб-приложение в Azure.

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

Выполните публикацию в удаленную службу приложений Azure, чтобы развернуть приложение. После этого введите пароль, указанный ранее при создании пользователя развертывания. Убедитесь, что вы ввели пароль, созданный в разделе [Настройка пользователя развертывания](#configure-a-deployment-user), а не тот, что использовался для входа на портал Azure.

```bash
git push azure master
```

Во время развертывания служба приложений Azure будет взаимодействовать с Git.

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Переход в приложение

Перейдите в развертываемое приложение с помощью веб-браузера.

```bash
http://<app_name>.azurewebsites.net
```

Теперь страница, на которой отображается сообщение Hello World, выполняется с использованием нашего кода Node.js как веб-приложение службы приложений Azure.

## <a name="updating-and-deploying-the-code"></a>Обновление и развертывание кода

В локальном текстовом редакторе в приложении Node.js откройте файл `index.js` и внесите небольшое изменение в текстовой строке рядом с вызовом `response.end`:

```nodejs
response.end("Hello Azure!");
```

Зафиксируйте изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git commit -am "updated output"
git push azure master
```

После завершения развертывания переключитесь в окно браузера, открытое на этапе **перехода в приложение**, и щелкните "Обновить".

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Управление новым веб-приложением Azure

Перейдите на портал Azure, чтобы просмотреть созданное приложение.

Для этого войдите на портал [https://portal.azure.com](https://portal.azure.com).

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Вы попадете в _колонку_ веб-приложения (страница портала, открывшаяся горизонтально).

По умолчанию колонка веб-приложения отображает страницу **обзора**. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. Вкладки в левой области колонки отображают разные страницы конфигурации, которые вы можете открыть.

![Колонка службы приложений на портале Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

На вкладках в колонке содержится множество полезных функций, которые вы можете добавить в веб-приложение. Ниже представлены лишь некоторые из возможностей:

* сопоставление настраиваемого DNS-имени;
* привязка настраиваемого SSL-сертификата;
* настройка непрерывного развертывания;
* вертикальное и горизонтальное масштабирование;
* добавление аутентификации пользователей.

**Поздравляем!** Вы развернули свое первое приложение Node.js в службе приложений.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

> [!div class="nextstepaction"]
> [Примеры Azure CLI](app-service-cli-samples.md)

