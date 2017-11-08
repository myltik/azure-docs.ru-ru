---
title: "Создание приложения .NET Core и его развертывание в службе приложений Azure на платформе Linux | Документация Майкрософт"
description: "Быстрое развертывание первого приложения Hello World на .NET Core в службе приложений на платформе Linux."
keywords: "служба приложений azure, веб-приложение, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 2a8000cadd6f6d7204e1790df62443a7ac7598c9
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2017
---
# <a name="create-a-net-core-web-app-in-app-service-on-linux"></a>Создание веб-приложения .NET Core в службе приложений на платформе Linux

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. В этом кратком руководстве показано, как создать приложение [.NET Core](https://docs.microsoft.com/aspnet/core/) в службе приложений на платформе Linux. Создайте веб-приложение с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) и разверните код .NET Core в веб-приложении с помощью Git.

![Пример приложения, выполняющегося в Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

* [установите Git](https://git-scm.com/);
* [установите пакет SDK для .NET Core](https://www.microsoft.com/net/download/core).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-app-locally"></a>Локальное создание приложения

В окне терминала на компьютере создайте каталог `hellodotnetcore` и перейдите в него.

```bash
md hellodotnetcore
cd hellodotnetcore
```

Создайте веб-приложение .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Локальный запуск приложения

Восстановите пакеты NuGet и запустите приложение.

```bash
dotnet restore
dotnet run
```

Откройте веб-браузер и перейдите к приложению в `http://localhost:5000`.

На странице отобразится сообщение **Hello World** из примера приложения.

![Тестирование с помощью браузера](media/quickstart-dotnetcore/dotnet-browse-local.png)

В окне терминала нажмите клавиши **CTRL+C**, чтобы выйти из веб-сервера. Инициализируйте репозиторий Git для проекта .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-with-built-in-image"></a>Создание веб-приложения со встроенным образом

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)]

Перейдите к только что созданному веб-приложению. Замените _&lt;app name>_ уникальным именем приложения.

```bash
http://<app name>.azurewebsites.net
```

![Пустая страница веб-приложения](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Переход в приложение

Перейдите в развертываемое приложение с помощью веб-браузера.

```bash
http://<app_name>.azurewebsites.net
```

Пример кода Node.js выполняется в веб-приложении со встроенным образом.

![Пример приложения, выполняющегося в Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Поздравляем!** Вы развернули свое первое приложение Node.js в службе приложений в Linux.

## <a name="update-and-redeploy-the-code"></a>Обновление и повторное развертывание кода

В локальном каталоге откройте файл _Startup.cs_. Внесите некоторые изменения в текст в вызове метода `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Зафиксируйте изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git commit -am "updated output"
git push azure master
```

После завершения развертывания переключитесь в окно браузера, открытое на этапе **перехода в приложение**, и щелкните "Обновить".

![Обновленный пример приложения, выполняющегося в Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-web-app"></a>Управление новым веб-приложением Azure

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> для управления созданным веб-приложением.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/quickstart-dotnetcore/portal-app-service-list.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. 

![Страница службы приложений на портале Azure](media/quickstart-dotnetcore/portal-app-overview.png)

В меню слева доступно несколько страниц для настройки приложения. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание веб-приложения .NET Core с базой данных SQL в службе приложений Azure в Linux](tutorial-dotnetcore-sqldb-app.md)
