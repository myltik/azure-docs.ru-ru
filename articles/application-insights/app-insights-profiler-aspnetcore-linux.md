---
title: Профилирование веб-приложений ASP.NET Core в Azure для Linux с помощью Application Insights Profiler | Документация Майкрософт
description: Обзор концепции и пошаговое руководство по ее реализации
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 63a7ceacffe1ee33227d3a8272dda7de7b3b1135
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Профилирование веб-приложений ASP.NET Core в Azure для Linux с помощью Application Insights Profiler

Эта функция сейчас доступна в режиме предварительной версии

Узнайте, сколько времени выполняется каждый метод в динамическом веб-приложении с помощью [Application Insights](app-insights-overview.md). Профилировщик теперь доступен для веб-приложений ASP.NET Core, размещенных в службах приложений для Linux. В этом руководстве описано, как выполнить сбор трассировок профилировщика для веб-приложений ASP.NET Core для Linux.

Когда вы завершите работу с этим руководством, ваше приложение будет собирать трассировки профилировщика, как на снимке экрана ниже. В этом примере трассировка профилировщика указывает на то, что определенный веб-запрос выполняется медленно, так как большая часть времени тратится на ожидание. Перед "горячим" путем в коде, который замедляет работу приложения, отображается значок пламени. В этом примере показано, что метод `About` в `HomeController` замедлил работу веб-приложения, так как вызывал `Thread.Sleep`.

![Трассировки профилировщика](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>Предварительные требования
Приведенные ниже инструкции применяются ко всем средам разработки для Windows, Linux и Mac:

* Установите [пакет SDK для .NET Core 2.1.2 или более поздней версии](https://www.microsoft.com/net/download/windows/build).
* Установите Git, следуя [руководству по началу работы и установке Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="setup-project-locally"></a>Локальная настройка проекта

1. Откройте командную строку на компьютере. Приведенные ниже инструкции работают для всех сред разработки для Windows, Linux и Mac.

2. Создайте веб-приложение MVC для ASP.NET.
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. Измените каталог в командной строке на корневую папку проекта.

4. Добавьте пакет Nuget для сбора трассировок профилировщика.
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. Добавьте строку кода для случайной задержки на несколько секунд в файле HomeController.cs.

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. Сохраните и зафиксируйте внесенные изменения в локальном репозитории.

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>Создание службы приложений Azure для размещения проекта
1. Создайте среду Linux для службы приложений.

    ![Создание служб приложений для Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Создайте учетные данные для развертывания. Запишите пароль, он потребуется позже при развертывании приложения.

    ![Создание учетных данных для развертывания](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Выберите способ развертывания. Настройте локальный репозиторий Git в веб-приложении, следуя инструкциям на портале Azure. Репозиторий Git будет создан автоматически.

    ![Настройка репозитория Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

См. [дополнительные варианты развертывания](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Развертывание проекта

1. В командной строке перейдите в корневую папку проекта. Добавьте удаленный репозиторий Git, чтобы он указывал на репозиторий в службах приложений:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * Используйте значение username, указанное на этапе создания учетных данных для развертывания.
    * Используйте значение app_name, указанное на этапе создания службы приложений.

2. Разверните проект, применив изменения в Azure с помощью команды push.

    ```
    git push azure master
    ```
Должен появиться похожий результат:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Добавление Application Insights для мониторинга веб-приложений
1. [Создайте ресурс Application Insights](./app-insights-create-new-resource.md).
2. Скопируйте ключ iKey ресурса Application Insights и задайте следующие параметры в службах приложений.

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Настройка параметров приложения](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    При изменении параметров приложения будет выполнен автоматический перезапуск сайта. После применения новых параметров профилировщик немедленно запустится на 2 минуты. Затем он будет запускаться на 2 минуты каждый час.

3. Создайте трафик для веб-сайта. Можно несколько раз обновить страницу ```About``` сайта.

4. Подождите 2–5 минут для статистической обработки событий в Application Insights.

5. Перейдите в область производительности Application Insights на портале Azure. В правом нижнем углу будут доступны трассировки профилировщика.

    ![Просмотр трассировок](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Известные проблемы

### <a name="enable-button-in-profiler-configuration-pane-does-not-work"></a>Кнопка включения на панели конфигурации профилировщика не работает
**При размещении приложения с помощью среды Linux для службы приложений снова включать профилировщик на панели производительности на портале App Insights не нужно. Для включения профилировщика достаточно включить пакет NuGet в проект и указать ключ iKey для App Insights в настройках приложения**.

Если вы следуете рабочему процессу [Профилировщик App Insights для Windows](./app-insights-profiler.md), чтобы нажать на кнопку **Включить** на панели настройки профилировщика, появится сообщение об ошибке, как будет предпринята попытка установки версию агента профилировщика Windows в среде Linux.

Мы работаем над устранением этой проблемы.

![Вам не нужно снова включать профилировщик на панели производительности, чтобы активировать его для работы со службами приложений Linux.](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Дальнейшие действия
Если вы используете пользовательские контейнеры, размещенные в службе приложений, чтобы включить Application Insights Profiler, следуйте инструкциям из статьи о [включении профилировщика службы для контейнерного приложения ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp).

При наличии каких-либо проблем или предложений сообщите об этом в наш репозиторий Github: [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues) (ApplicationInsights-Profiler-AspNetCore: вопросы)
