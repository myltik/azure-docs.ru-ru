---
title: Создание статического веб-приложения HTML в Azure | Документация Майкрософт
description: Узнайте, как запускать веб-приложения в службе приложений Azure, развернув пример статического HTML-приложения.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/26/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: bca5757c971f15279ed6ee9b41f415cd347d91b3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28918783"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Создание статического веб-приложения HTML в Azure

[Веб-приложения Azure](app-service-web-overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости.  В этом кратком руководстве объясняется, как развернуть базовый сайт HTML+CSS в веб-приложениях Azure. Создайте веб-приложение с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) и разверните пример HTML-содержимого в веб-приложении с помощью Git.

![Домашняя страница в примере приложения](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux. После установки необходимых компонентов для выполнения этих шагов потребуется около пяти минут.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством сделайте следующее:

- <a href="https://git-scm.com/" target="_blank">установите Git</a>;

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

Перейдите в каталог, в котором содержится образец кода.

```bash
cd html-docs-hello-world
```

## <a name="view-the-html"></a>Просмотр HTML

Перейдите в каталог, в котором содержится пример HTML. Откройте файл *index.html* в браузере.

![Домашняя страница примера приложения](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)] 

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app.md)] 

![Пустая страница веб-приложения](media/app-service-web-get-started-html/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Переход в приложение

В браузере перейдите по URL-адресу веб-приложения Azure: `http://<app_name>.azurewebsites.net`.

Страница выполняется как веб-приложение службы приложений Azure.

![Домашняя страница примера приложения](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

**Поздравляем!** Вы развернули свое первое HTML-приложение в службе приложений.

## <a name="update-and-redeploy-the-app"></a>Обновление и повторное развертывание приложения

Откройте файл *index.html* в текстовом редакторе и измените разметку. Например, измените заголовок H1 "Служба приложений Azure — пример статического HTML-сайта" на "Служба приложений Azure".

В окне терминала на локальном компьютере зафиксируйте изменения в Git, а затем отправьте изменение кода в Azure.

```bash
git commit -am "updated HTML"
git push azure master
```

Завершив развертывание, обновите страницу в браузере, чтобы увидеть изменения.

![Обновленная домашняя страница примера приложения](media/app-service-web-get-started-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-web-app"></a>Управление новым веб-приложением Azure

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> для управления созданным веб-приложением.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-get-started-html/portal1.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. 

![Колонка службы приложений на портале Azure](./media/app-service-web-get-started-html/portal2.png)

В меню слева доступно несколько страниц для настройки приложения. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сопоставление пользовательского домена](app-service-web-tutorial-custom-domain.md)
