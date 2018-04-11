---
title: Azure Application Insights для ASP.NET Core | Документация Майкрософт
description: Отслеживайте доступность, производительность и использование веб-приложений.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: 3f57eb3ede3b57e28c13e053b86ab022fdd21b7e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights для ASP.NET Core

Azure Application Insights обеспечивает детализированный мониторинг веб-приложения на уровне кода. Вы можете легко отслеживать доступность, производительность и использование своего веб-приложения. Вы также можете быстро идентифицировать и диагностировать ошибки в приложении, не дожидаясь, пока пользователь сообщит о них.

Эта статья содержит сведения о создании примера приложения ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) в Visual Studio и о том, как начать выполнять мониторинг с помощью Azure Application Insights.

## <a name="prerequisites"></a>предварительным требованиям

- Пакет SDK для NET Core 2.0.0 или более поздней версии.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) версии 15.3 или выше с рабочей нагрузкой "ASP.NET и веб-разработка".

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Создание проекта ASP.NET Core в Visual Studio

1. Щелкните правой кнопкой мыши и запустите **Visual Studio 2017** от имени администратора.
2. Выберите **Файл** > **Создать** > **Проект** (CTRL+SHIFT+N).

   ![Снимок экрана: меню "Файл", "Создать", "Проект" в Visual Studio](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Разверните **Visual C#**, выберите **.NET Core** > **Веб-приложение ASP.NET Core**. Заполните поля **Имя** > **Имя решения** и установите флажок **Создать новый репозиторий Git**.

   ![Снимок экрана: меню "Файл", пункт "Создать", "Новый проект" в Visual Studio](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Выберите **.NET Core** > **ASP.NET Core 2.0** и **Веб-приложение** > **ОК**.

    ![Снимок экрана: "Файл", "Новый проект", меню выбора в Visual Studio](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Добавление телеметрии Application Insights

1. Выберите **Проект** > **Добавить телеметрию Application Insights...** (Кроме того, вы можете щелкнуть правой кнопкой мыши **Подключенные службы** и выбрать "Добавить подключенную службу".)

    ![Снимок экрана: "Файл", "Новый проект", меню выбора в Visual Studio](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Выберите **Начать бесплатно**.

    ![Снимок экрана: "Файл", "Новый проект", меню выбора в Visual Studio](./media/app-insights-asp-net-core/0005-start-free.png)

3. Выберите соответствующую **подписку** > **ресурс** и укажите, разрешать ли сбор данных объемом свыше 1 ГБ в месяц. Затем нажмите кнопку **Зарегистрировать**.

    ![Снимок экрана: "Файл", "Новый проект", меню выбора в Visual Studio](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>Изменения, внесенные в проект

Application Insights имеет очень низкие издержки. Чтобы просмотреть изменения, внесенные в ваш проект путем добавления телеметрии Application Insights, сделайте следующее:

Выберите **Вид** > **Командный обозреватель** (CTRL+\, CTRL+M) > **Проект** > **Изменения**.

- Всего четыре изменения:

  ![Снимок экрана файлов, измененных путем добавления Application Insights](./media/app-insights-asp-net-core/0007-changes.png)

- Создан один файл:

   **ConnectedService.json**

  ![Снимок экрана файлов, измененных путем добавления Application Insights](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Три файла изменены:

  **appsettings.json**

   ![Снимок экрана файлов, измененных путем добавления Application Insights](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Снимок экрана файлов, измененных путем добавления Application Insights](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Снимок экрана файлов, измененных путем добавления Application Insights](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Создание искусственных транзакций с помощью PowerShell

Чтобы выполнить тестирование трафика, запустите приложение и щелкайте ссылки вручную. Тем не менее часто бывает полезно создать простую искусственную транзакцию в PowerShell.

1. Запустите приложение, щелкнув IIS Express. ![Снимок экрана: значок IIS Express в Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Скопируйте URL-адрес из адресной строки браузера. Он имеет формат http://localhost:{random номер порта}.

   ![Снимок экрана: URL-адрес в адресной строке браузера](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Запустите указанный ниже цикл PowerShell, чтобы создать 100 искусственных транзакций для тестированного приложения. Измените номер порта, указанный после **localhost:**, в соответствии с URL-адресом, скопированным на предыдущем шаге.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Открытие портала Application Insights

После выполнения скрипта PowerShell в предыдущем разделе запустите Application Insights, чтобы просмотреть транзакции, и убедитесь, что данные собраны. 

В меню Visual Studio выберите **Проект** > **Application Insights** > **Открыть портал Application Insights**.

   ![Снимок экрана: обзор Application Insights](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> На снимке экрана с примером выше данные для параметров **Live Stream**, **Время загрузки страницы** и **Невыполненные запросы** не собраны. В следующем разделе описано добавление всех этих данных. Если вы уже собрали данные для **Live Stream** и **Время загрузки страницы**, следуйте указаниям для параметра **Невыполненные запросы**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Сбор данных для параметров "Невыполненные запросы", Live Stream и "Время загрузки страницы"

### <a name="failed-requests"></a>Невыполненные запросы

С технической точки зрения данные **невыполненных запросов** собираются, но еще не отображаются. Чтобы ускорить процесс, можно добавить пользовательское исключение к имеющемуся проекту для имитации реального исключения. Если приложение все еще запущено в Visual Studio, прежде чем **остановить отладку** (SHIFT+F5), сделайте следующее:

1. В **обозревателе решений** разверните **Страницы** > **About.cshtml** и откройте **About.cshtml.cs**.

   ![Снимок экрана: обозреватель решений Visual Studio](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. Добавьте исключение под строкой ``Message=`` и сохраните изменения в файле.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Снимок экрана кода исключения](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Live Stream

Чтобы получить доступ к функции Live Stream в Application Insights с помощью ASP.NET Core, обновите пакеты NuGet до версии **Microsoft.ApplicationInsights.AspNetCore 2.2.0**.

В Visual Studio выберите **Проект** > **Управление пакетами NuGet** > **Microsoft.ApplicationInsights.AspNetCore**, в поле "Версия" введите **2.2.0**  >  и нажмите кнопку **Обновить**.

  ![Снимок экрана: диспетчер пакетов NuGet](./media/app-insights-asp-net-core/0017-update-nuget.png)

Отобразятся несколько запросов на подтверждение, прочтите и примите условия, если вы согласны с изменениями.

### <a name="page-view-load-time"></a>Время загрузки страницы

1. В Visual Studio выберите **Обозреватель решений** > **Страницы**. Необходимо будет изменить два файла: **_Layout.cshtml** и **_ViewImports.cshtml**.

2. В файле **_ViewImports.cshtml** добавьте следующее:

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Снимок экрана: изменение кода в _ViewImports.cshtml](./media/app-insights-asp-net-core/00018-view-imports.png)

3. В файле **Layout.cshtml** добавьте указанную ниже строку перед тегом ``</head>`` и перед любыми другими сценариями.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Снимок экрана: изменение кода в layout.cshtml](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Тестирование сбора данных для параметров "Невыполненные запросы", "Время загрузки страницы" и Live Stream

Теперь вы можете выполнить тестирование и убедиться, что все работает правильно.

1. Запустите приложение, щелкнув IIS Express. ![Снимок экрана: значок IIS Express в Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Перейдите на страницу **О программе**, чтобы активировать исключения теста. (Если вы выполняете запуск в режиме отладки, щелкните **Продолжить** в Visual Studio до того, как исключение будет выбрано в Application Insights.)

3. Перезапустите скрипт имитированной транзакции PowerShell (см. выше). (Возможно, вам потребуется изменить в нем номер порта.)

4. Если окно обзора Applications Insights еще не открыто, в меню Visual Studio выберите **Проект** > **Application Insights** > **Открыть портал Application Insights**. 

   > [!TIP]
   > Если новый трафик не отображается, проверьте заданный **диапазон времени** и нажмите кнопку **Обновить**.

   ![Снимок экрана: окно обзора](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Выберите Live Stream

   ![Снимок экрана: Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Если скрипт PowerShell все еще выполняется, отобразятся динамические метрики, если он остановлен, снова запустите скрипт с открытой службой Live Stream.)

## <a name="app-insights-sdk-comparison"></a>Сравнение наличия функций в пакетах SDK App Insights

Группа по продукту Application Insights работала над тем, чтобы добиться максимальной четности функциональных возможностей между [полным пакетом SDK для .NET Framework](https://github.com/Microsoft/ApplicationInsights-dotnet) и пакетом SDK для .NET Core. В [пакете SDK для ASP.NET Core версии 2.2.0](https://github.com/Microsoft/ApplicationInsights-aspnetcore) для Application Insights максимально минимизировано различие между функциями.

Чтобы узнать больше о различиях и недостатках [.NET и .NET Core](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server), просмотрите следующую таблицу.

   | Сравнение пакетов SDK | ASP.NET:        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Динамические метрики**      | **+** |**-** | **+** |
   | **Канал телеметрии сервера** | **+** |**-** | **+**|
   |**Адаптивная выборка**| **+** | **-** | **+**|
   | **Вызовов зависимостей SQL**     | **+** |**-** | **+**|
   | **Счетчики производительности*** | **+** | **-**| **-**|

_Счетчики производительности_ в этом контексте относятся к [счетчикам производительности на стороне сервера](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters), например использование процессора, памяти и диска.

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
[Чтение кода и дополнительные наработки](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Дополнительная информация
* [Изучите маршруты пользователей](app-insights-usage-flows.md), чтобы понять, как пользователи перемещаются приложением.
* [Настройка коллекции моментальных снимков](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) для отображения состояния исходного кода и переменных в момент создания исключения.
* [Используйте API](app-insights-api-custom-events-metrics.md) для отправки собственных событий и метрик для более четкого представления о производительности и использовании приложения.
* [Тесты доступности](app-insights-monitor-web-app-availability.md) позволяют постоянно проверять работу приложения из всех точек мира.
