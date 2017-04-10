---
title: "Создание приложения Node.js в веб-приложении | Документация Майкрософт"
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
ms.date: 03/28/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 22966659db3951316dca11fd555e07a183114062
ms.lasthandoff: 04/04/2017

---
# <a name="create-a-nodejs-application-on-web-app"></a>Создание приложения Node.js в веб-приложении

В этом кратком руководстве описано, как разработать и развернуть приложение Node.js в Azure. Мы запустим приложение, используя службу приложений Azure под управлением Linux, а затем создадим и настроим в ней новое веб-приложение с помощью интерфейса командной строки Azure. Затем мы используем Git, чтобы развернуть наше приложение Node.js в Azure.

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux. На все это вам понадобится около 5 минут.

## <a name="before-you-begin"></a>Перед началом работы

Перед запуском этого примера необходимо скачать и установить следующие компоненты в локальной среде:

1. [Git.](https://git-scm.com/)
1. [Node.js и NPM.](https://nodejs.org/)
1. [Azure CLI 2.0.](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

Клонируйте репозиторий примера приложения Hello World на локальный компьютер.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

> [!TIP]
> Можно также [скачать пример](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) в виде ZIP-файла и извлечь его.

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

## <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

Для использования FTP и локального репозитория Git на сервере необходимо настроить пользователя развертывания, чтобы выполнить проверку подлинности развертывания. Создание пользователя развертывания выполняется однократно. Запишите имя пользователя и пароль, так как они потребуются нам в дальнейшем.

> [!NOTE]
> Пользователь развертывания необходим для развертывания FTP и локального репозитория Git в веб-приложении.
> `username` и `password` являются учетными данными на уровне учетной записи и отличаются от учетных данных подписки Azure. Эти учетные данные создаются только один раз.
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

Создайте план службы приложений на базе Linux с помощью команды [az appservice plan create](/cli/azure/appservice/plan#create).

> [!NOTE]
> План службы приложений представляет собой коллекцию физических ресурсов, используемых для размещения приложений. Все приложения, назначенные плану службы приложений, совместно используют ресурсы, определенные в нем. Поэтому, разместив несколько приложений, вы сможете сэкономить.
>
> Планы службы приложений определяют такие компоненты:
> * регион (Северная Европа, восточная часть США, Юго-Восточная Азия);
> * размер экземпляра (небольшой, средний, крупный);
> * число масштабируемых элементов (один, два, три экземпляра и т. д.);
> * SKU ("Бесплатный", "Общий", "Базовый", "Стандартный", "Премиум").
>

В следующем примере создается план службы приложений с именем `quickStartPlan` ценовой категории **Standard** в рабочих ролях Linux.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku S1 --is-linux
```

После создания плана службы приложений в Azure CLI отображается информация следующего вида.

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "kind": "linux",
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

План службы приложений создан. Теперь создайте веб-приложение в рамках этого плана (`quickStartPlan`). Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Используйте команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение.

В приведенной ниже команде замените заполнитель <app_name> уникальным именем приложения. <app_name> будет использоваться по умолчанию в качестве сайта DNS для веб-приложения. Поэтому это имя должно быть уникальным для всех приложений в Azure. Позже можно сопоставить любые пользовательские записи DNS с веб-приложением, прежде чем предоставлять его пользователям.

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

Мы создали пустое веб-приложение в Azure. Теперь настроим в веб-приложении использование языка Node.js и развернем в него наше приложение.

## <a name="configure-to-use-nodejs"></a>Настройка использования Node.js в веб-приложении

Используйте команду [az appservice web config update](/cli/azure/app-service/web/config#update), чтобы настроить в веб-приложении использование Node.js версии `6.9.3`.

> [!TIP]
> При такой настройке версии Node.js будет использоваться контейнер по умолчанию, предоставляемый платформой. Если вы хотите использовать собственный контейнер, ознакомьтесь с командой [az appservice web config container update](/cli/azure/appservice/web/config/container#update) в справочнике по интерфейсу командной строки CLI.

```azurecli
az appservice web config update --linux-fx-version "NODE|6.9.3" --startup-file process.json --name <app_name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>Настройка локального развертывания Git

Для развертывания в веб-приложение можно использовать FTP, локальный репозиторий Git, GitHub, Visual Studio Team Services и Bitbucket.

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

После завершения развертывания переключитесь в окно браузера, открытое на этапе перехода в приложение, и нажмите кнопку "Обновить".

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Управление новым веб-приложением Azure

Перейдите на портал Azure, чтобы просмотреть созданное приложение.

Для этого войдите на портал [https://portal.azure.com](https://portal.azure.com).

В меню слева выберите **Служба приложений**, а затем щелкните имя своего веб-приложения Azure.

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

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с предварительно созданными [скриптами интерфейса командной строки для веб-приложений](app-service-cli-samples.md).
