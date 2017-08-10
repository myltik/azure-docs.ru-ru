---
title: "Отладчик моментальных снимков Azure Application Insights для приложений .NET | Документация Майкрософт"
description: "Отладочные моментальные снимки автоматически собираются при порождении исключений в рабочих приложениях .NET"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: dcc5cc0be4c03ad661cf1539cb98a7d4fc94e778
ms.contentlocale: ru-ru
ms.lasthandoff: 07/19/2017

---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Отладочные моментальные снимки для исключений в приложениях .NET

При возникновении исключения, можно автоматически собирать отладочный моментальный снимок из работающего веб-приложения. Моментальный снимок отображает состояние исходного кода и переменных в момент порождения этого исключения. Отладчик моментальных снимков (предварительная версия) в [Azure Application Insights](app-insights-overview.md) отслеживает телеметрию исключений, поступающую из веб-приложения. Он собирает моментальные снимки для наиболее частых исключений, чтобы предоставить вам необходимые сведения для диагностики проблем в рабочей среде. Добавьте [пакет NuGet сборщика моментальных снимков](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) в приложение и, при необходимости, настройте параметры сбора в файле [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Моментальные снимки для [исключений](app-insights-asp-net-exceptions.md) отображаются на портале Application Insights.

Вы можете просмотреть отладочные моментальные снимки на портале, чтобы изучить стек вызовов и проверить значения переменных в каждом кадре стека вызовов. Чтобы воспользоваться более мощными средствами отладки исходного кода, откройте моментальные снимки в Visual Studio 2017 Enterprise, [скачав расширение отладчика моментальных снимков для Visual Studio](https://aka.ms/snapshotdebugger).

Коллекция моментальных снимков доступна для:
* .NET Framework и приложений ASP.NET выполняющихся с помощью .NET Framework 4.5 или более поздней версии.
* .NET Core 2.0 и приложений ASP.NET Core 2.0 под управлением Windows.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Настройка сбора моментальных снимков для приложений

1. [Включите Application Insights в веб-приложении](app-insights-asp-net.md), если вы еще не сделали это.

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector). 

3. Просмотрите параметры по умолчанию, добавленные этим пакетом в файл [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in one minute. -->
        <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Моментальные снимки собираются только для исключений, которые передаются в Application Insights. В некоторых случаях (например, в более старых версиях платформы .NET) может потребоваться [настроить сбор исключений](app-insights-asp-net-exceptions.md#exceptions), чтобы просматривать исключения с помощью моментальных снимков, которые отображаются на портале.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Настройка сбора моментальных снимков для приложений ASP.NET Core 2.0

1. [Включите Application Insights в веб-приложении ASP.NET Core](app-insights-asp-net-core.md), если вы еще не сделали это.

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. Измените метод `ConfigureServices` в классе `Startup` приложения, чтобы добавить обработчик телеметрии сборщика моментальных снимков. Код, который следует добавить, зависит от указанной версии пакета NuGet Microsoft.ApplicationInsights.ASPNETCore.

   Для Microsoft.ApplicationInsights.AspNetCore 2.1.0 добавьте:
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddSingleton<Func<ITelemetryProcessor, ITelemetryProcessor>>(next => new SnapshotCollectorTelemetryProcessor(next));
           // TODO: Add any other services your application needs here.
       }
   }
   ```

   Для Microsoft.ApplicationInsights.AspNetCore 2.1.1 добавьте:
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           public ITelemetryProcessor Create(ITelemetryProcessor next) =>
               new SnapshotCollectorTelemetryProcessor(next);
       }

       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
            services.AddSingleton<ITelemetryProcessorFactory>(new SnapshotCollectorTelemetryProcessorFactory());
           // TODO: Add any other services your application needs here.
       }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Настройка сбора моментальных снимков для других приложений .NET

1. Если приложение еще не инструментировано с помощью Application Insights, начните с [включения Application Insights и установки ключа инструментирования](app-insights-windows-desktop.md).

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. Моментальные снимки собираются только для исключений, которые передаются в Application Insights. Для их передачи может потребоваться изменить код. Код обработки исключений зависит от структуры приложения. Пример приведен ниже:
   ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }

## Grant permissions

Owners of the Azure subscription can inspect snapshots. Other users must be granted permission by an owner.

To grant permission, assign the `Application Insights Snapshot Debugger` role to users who will inspect snapshots. This role can be assigned to individual users or groups by subscription owners for the target Application Insights resource or its resource group or subscription.

1. Open the Access Control (IAM) blade.
1. Click the +Add button.
1. Select Application Insights Snapshot Debugger from the Roles drop-down list.
1. Search for and enter a name for the user to add.
1. Click the Save button to add the user to the role.


[!IMPORTANT]
    Snapshots can potentially contain personal and other sensitive information in variable and parameter values.

## Debug snapshots in the Application Insights portal

If a snapshot is available for a given exception or a problem ID, an **Open Debug Snapshot** button appears on the [exception](app-insights-asp-net-exceptions.md) in the Application Insights portal.

![Open Debug Snapshot button on exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

In the Debug Snapshot view, you see a call stack and a variables pane. When you select frames of the call stack in the call stack pane, you can view local variables and parameters for that function call in the variables pane.

![View Debug Snapshot in the portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Snapshots might contain sensitive information, and by default they are not viewable. To view snapshots, you must have the `Application Insights Snapshot Debugger` role assigned to you.

## Debug snapshots with Visual Studio 2017 Enterprise
1. Click the **Download Snapshot** button to download a `.diagsession` file, which can be opened by Visual Studio 2017 Enterprise. 

2. To open the `.diagsession` file, you must first [download and install the Snapshot Debugger extension for Visual Studio](https://aka.ms/snapshotdebugger).

3. After you open the snapshot file, the Minidump Debugging page in Visual Studio appears. Click **Debug Managed Code** to start debugging the snapshot. The snapshot opens to the line of code where the exception was thrown so that you can debug the current state of the process.

    ![View debug snapshot in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

The downloaded snapshot contains any symbol files that were found on your web application server. These symbol files are required to associate snapshot data with source code. For App Service apps, make sure to enable symbol deployment when you publish your web apps.

## How snapshots work

When your application starts, a separate snapshot uploader process is created that monitors your application for snapshot requests. When a snapshot is requested, a shadow copy of the running process is made in about 10 to 20 minutes. The shadow process is then analyzed, and a snapshot is created while the main process continues to run and serve traffic to users. The snapshot is then uploaded to Application Insights along with any relevant symbol (.pdb) files that are needed to view the snapshot.

## Current limitations

### Publish symbols
The Snapshot Debugger requires symbol files on the production server to decode variables and to provide a debugging experience in Visual Studio. The 15.2 release of Visual Studio 2017 publishes symbols for release builds by default when it publishes to App Service. In prior versions, you need to add the following line to your publish profile `.pubxml` file so that symbols are published in release mode:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Для среды вычислений Azure и других типов сред убедитесь, что файлы символов находятся в той же папке, что и библиотеки DLL основного приложения (обычно это `wwwroot/bin`), или доступны по текущему пути.

### <a name="optimized-builds"></a>Оптимизированные сборки
В некоторых случаях локальные переменные не могут отображаться в сборках выпуска из-за оптимизаций, примененных во время процесса сборки.

## <a name="troubleshooting"></a>Устранение неполадок

Эти советы помогут при устранении проблем, связанных с отладчиком моментальных снимков.

### <a name="verify-the-instrumentation-key"></a>Проверка ключа инструментирования

Убедитесь, что в опубликованном приложении используется правильный ключ инструментирования. Как правило, Application Insights счтывает ключ инструментирования из файла ApplicationInsights.config. Убедитесь, что его значение такое же, что и у ключа инструментирования для ресурса Application Insights, который отображается на портале.

### <a name="check-the-uploader-logs"></a>Проверьте журналы отправителя

После создания моментального снимка на диске создается файл минидампа (.dmp). Этот файл минидампа и любые связанные PDB-файлы отправляются в отдельном процессе передачи в хранилище отладчика моментальных снимков Application Insights. После успешной передачи минидамп удаляется с диска. Файлы журнала для отправителя минидампа сохраняются на диске. В среде службы приложений эти журналы можно найти в `D:\Home\LogFiles\Uploader_*.log`. Используйте веб-сайт управления Kudu для службы приложений, чтобы найти эти файлы журнала.

1. Откройте службу приложений на портале Azure.

2. Щелкните колонку **Дополнительные инструменты** или найдите **Kudu**.
3. Щелкните **Переход**.
4. В раскрывающемся списке **Debug console** (Консоль отладки) выберите **CMD** (Команда).
5. Щелкните **LogFiles**.

Вы увидите хотя бы один файл с именем, начинающимся с `Uploader_` и расширением `.log`. Щелкните соответствующую пиктограмму, чтобы скачать все файлы журналов, или открыть их в браузере.
Имя файла содержит имя компьютера. Если экземпляр службы приложений размещен на нескольких компьютерах, для каждого компьютера существуют отдельные файлы журналов. Когда отправитель обнаруживает новый файл минидампа, он записывается в файл журнала. Ниже приведен пример успешной отправки:

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

В предыдущем примере, ключ инструментирования — это `c12a605e73c44346a984e00000000000`. Это значение должно соответствовать ключу инструментирования для вашего приложения.
Минидамп связан с моментальным снимком с идентификатором `139e411a23934dc0b9ea08a626db16c5`. Позже этот идентификатор можно использовать для поиска связанной телеметрии исключений в аналитике Application Insights.

Отправитель проверяет наличие новых PDB-файлов примерно один раз каждые 15 минут. Ниже приведен пример:

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

Для приложений, которые _не_ размещаются в службе приложений, журналы отправителя находятся в той же папке минидампов: `%TEMP%\Dumps\<ikey>` (где `<ikey>` ваш ключ инструментирования).

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Поиск исключений с моментальными снимками с помощью поиска Application Insights

При создании моментального снимка вызванное исключение обозначается идентификатором моментального снимка. При передаче телеметрии исключения в Application Insights этот идентификатор моментального снимка включается в качестве пользовательского свойства. В колонке поиска Application Insights можно найти все данные телеметрии с пользовательским свойством `ai.snapshot.id`.

1. Перейдите к ресурсу Application Insights на портале Azure.
2. Щелкните **Search**(Поиск).
3. В текстовом поле поиска введите `ai.snapshot.id` и нажмите клавишу ВВОД.

![Поиск телеметрии с помощью идентификатора моментального снимка на портале](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Если этот поиск не дал результатов, значит в Application Insights не передавались моментальные снимки для приложения в выбранный диапазон времени.

Чтобы найти определенный идентификатор снимка из журналов отправителя, введите этот идентификатор в поле поиска. Если не удается найти данные телеметрии для моментального снимка, который был отправлен, выполните следующие действия:

1. Еще раз проверьте, что вы смотрите на правильный ресурс Application Insights при проверке ключа инструментирования.

2. С помощью метки времени из журнала отправителя, настройте фильтр диапазона времени поиска так, чтобы охватить этот диапазон времени.

Если исключение с таким идентификатором моментальных снимков по-прежнему не отображается, значит телеметрия исключения не отправлялась в Application Insights. Это происходит в случае сбоя приложения после снятия снимка, но до того как оно передало телеметрию исключений. В этом случае проверьте журналы службы приложений в `Diagnose and solve problems`, чтобы проверить, были ли неожиданные перезагрузки или необработанные исключения.

## <a name="next-steps"></a>Дальнейшие действия

* [Установите в коде точки прикрепления](https://azure.microsoft.com/blog/snapshot-debugger-for-azure/), чтобы получать моментальные снимки не ожидая исключений.
* В статье [Диагностика исключений в веб-приложениях с помощью Application Insights](app-insights-asp-net-exceptions.md) объясняется, как отобразить дополнительные исключения в Application Insights. 
* В статье [Интеллектуальное обнаружение в Application Insights](app-insights-proactive-diagnostics.md) описывается автоматическое обнаружение аномалий производительности.

