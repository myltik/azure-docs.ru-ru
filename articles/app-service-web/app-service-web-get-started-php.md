---
title: "Создание веб-приложения PHP в Azure | Документация Майкрософт"
description: "Разверните свое первое приложение Hello World на языке PHP в веб-приложении службы приложений Azure за считаные минуты."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/21/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 7667cf679821cc99d6e8b3ec4aa466067d8a6b32
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---
# <a name="create-a-php-web-app-in-azure"></a>Создание веб-приложения PHP в Azure

[Веб-приложения Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости.  В этом кратком руководстве объясняется, как развернуть приложение PHP в веб-приложениях Azure. Создайте веб-приложение с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) в Cloud Shell и разверните пример кода PHP в веб-приложении с помощью Git.

![Пример приложения, выполняющегося в Azure]](media/app-service-web-get-started-php/hello-world-in-browser.png)

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux. После установки необходимых компонентов для выполнения этих шагов потребуется около пяти минут.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

* [установите Git](https://git-scm.com/);
* [установите PHP](https://php.net).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample-locally"></a>Скачать пример на локальный компьютер

Выполните следующие команды в окне терминала. Так вы клонируете пример приложения на локальный компьютер и перейдете в каталог, содержащий пример кода.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Локальный запуск приложения

Запустите приложение в локальной среде, открыв окно терминала и выполнив команду `php`, чтобы запустить встроенный веб-сервер PHP.

```bash
php -S localhost:8080
```

Откройте браузер и перейдите к примеру приложения по адресу http://localhost:8080.

На странице отобразится сообщение **Hello World!** из примера приложения.

![Пример приложения, выполняющегося локально](media/app-service-web-get-started-php/localhost-hello-world-in-browser.png)

В окне терминала нажмите клавиши **CTRL+C**, чтобы выйти из веб-сервера.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app.md)]

![Пустая страница веб-приложения](media/app-service-web-get-started-php/app-service-web-service-created.png)

Вы создали пустое веб-приложение в Azure.

[!INCLUDE [Configure local git](../../includes/app-service-web-configure-local-git.md)] 

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app-locally"></a>Переход к приложению на локальном компьютере

Перейдите в развертываемое приложение с помощью веб-браузера.

```bash
http://<app_name>.azurewebsites.net
```

Пример кода PHP выполняется в веб-приложении службы приложений Azure.

![Пример приложения, выполняющегося в Azure](media/app-service-web-get-started-php/hello-world-in-browser.png)

**Поздравляем!** Вы развернули свое первое приложение PHP в службе приложений.

## <a name="update-locally-and-redeploy-the-code"></a>Обновление на локальном компьютере и повторное развертывание кода

В локальном текстовом редакторе в приложении PHP откройте файл `index.php` и внесите небольшое изменение в текстовой строке рядом с `echo`:

```php
echo "Hello Azure!";
```

Зафиксируйте изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git commit -am "updated output"
git push azure master
```

После завершения развертывания перейдите в окно браузера, открытое на шаге **перехода в приложение**, и обновите страницу.

![Обновленный пример приложения, выполняющегося в Azure](media/app-service-web-get-started-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Управление новым веб-приложением Azure

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> для управления созданным веб-приложением.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-get-started-php/php-docs-hello-world-app-service-list.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление.

![Колонка службы приложений на портале Azure](media/app-service-web-get-started-php/php-docs-hello-world-app-service-detail.png)

В меню слева доступно несколько страниц для настройки приложения. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Использование PHP и MySQL](app-service-web-tutorial-php-mysql.md)

