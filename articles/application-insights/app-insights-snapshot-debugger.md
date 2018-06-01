---
title: Отладчик моментальных снимков Azure Application Insights для приложений .NET | Документация Майкрософт
description: Отладочные моментальные снимки автоматически собираются при порождении исключений в рабочих приложениях .NET
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mbullwin; pharring
ms.openlocfilehash: 66339e5f5d2cc7447df0f8faf70d2d9fd45db738
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159141"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Отладочные моментальные снимки для исключений в приложениях .NET

При возникновении исключения, можно автоматически собирать отладочный моментальный снимок из работающего веб-приложения. Моментальный снимок отображает состояние исходного кода и переменных в момент порождения этого исключения. Отладчик моментальных снимков (предварительная версия) в [Azure Application Insights](app-insights-overview.md) отслеживает телеметрию исключений, поступающую из веб-приложения. Он собирает моментальные снимки для наиболее частых исключений, чтобы предоставить вам необходимые сведения для диагностики проблем в рабочей среде. Добавьте [пакет NuGet сборщика моментальных снимков](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) в приложение и, при необходимости, настройте параметры сбора в файле [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Моментальные снимки для [исключений](app-insights-asp-net-exceptions.md) отображаются на портале Application Insights.

Вы можете просмотреть отладочные моментальные снимки на портале, чтобы изучить стек вызовов и проверить значения переменных в каждом кадре стека вызовов. Чтобы воспользоваться более мощными средствами отладки исходного кода, откройте моментальные снимки в Visual Studio 2017 Enterprise, [скачав расширение отладчика моментальных снимков для Visual Studio](https://aka.ms/snapshotdebugger). В Visual Studio можно также [настроить точки прикрепления для интерактивного создания моментальных снимков](https://aka.ms/snappoint) без ожидания исключения.

Коллекция моментальных снимков доступна для:
* .NET Framework и приложений ASP.NET выполняющихся с помощью .NET Framework 4.5 или более поздней версии.
* .NET Core 2.0 и приложений ASP.NET Core 2.0 под управлением Windows.

Поддерживаются следующие среды:
* служба приложений Azure;
* облачная служба Azure под управлением ОС семейства 4 или более поздней версии;
* службы Azure Service Fabric под управлением Windows Server 2012 R2 или более поздней версии;
* виртуальные машины Azure под управлением Windows Server 2012 R2 или более поздней версии;
* локальные виртуальные машины или физические компьютеры под управлением Windows Server 2012 R2 или более поздней версии.

> [!NOTE]
> Клиентские приложения (например, WPF, Windows Forms или UWP) не поддерживаются.

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
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Моментальные снимки собираются только для исключений, которые передаются в Application Insights. В некоторых случаях (например, в более старых версиях платформы .NET) может потребоваться [настроить сбор исключений](app-insights-asp-net-exceptions.md#exceptions), чтобы просматривать исключения с помощью моментальных снимков, которые отображаются на портале.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Настройка сбора моментальных снимков для приложений ASP.NET Core 2.0

1. [Включите Application Insights в веб-приложении ASP.NET Core](app-insights-asp-net-core.md), если вы еще не сделали это.

    > [!NOTE]
    > Убедитесь, что приложение ссылается на версию 2.1.1 или более позднюю версию пакета Microsoft.ApplicationInsights.AspNetCore.

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. Чтобы добавить и настроить обработчик телеметрии сборщика моментальных снимков, измените класс `Startup` своего приложения.

    Добавьте указанные ниже операторы using в `Startup.cs`.

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Добавьте указанный ниже класс `SnapshotCollectorTelemetryProcessorFactory` в класс `Startup`.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Добавьте службы `SnapshotCollectorConfiguration` и `SnapshotCollectorTelemetryProcessorFactory` в конвейер запуска:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Настройте сборщик моментальных снимков, добавив раздел SnapshotCollectorConfiguration в файл appsettings.json. Например: 

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Настройка сбора моментальных снимков для других приложений .NET

1. Если приложение еще не инструментировано с помощью Application Insights, начните с [включения Application Insights и установки ключа инструментирования](app-insights-windows-desktop.md).

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. Моментальные снимки собираются только для исключений, которые передаются в Application Insights. Для их передачи может потребоваться изменить код. Код обработки исключений зависит от структуры приложения. Пример приведен ниже:
    ```csharp
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
    ```

## <a name="grant-permissions"></a>Предоставление разрешений

Владельцы подписок Azure могут проверять моментальные снимки. Другие пользователи должны иметь разрешение предоставленное владельцем.

Чтобы предоставить разрешение, назначьте роль `Application Insights Snapshot Debugger` пользователям, которые будут проверять моментальные снимки. Владельцы подписок могут назначить эту роль отдельным пользователям или группам для целевого ресурса Application Insights или его группы ресурсов или подписки.

1. Перейдите к ресурсу Application Insights на портале Azure.
1. Выберите **Управление доступом (IAM)**.
1. Нажмите кнопку **+ Добавить**.
1. В раскрывающемся списке **Роли** выберите **Отладчик моментальных снимков Application Insights**.
1. Выполните поиск и введите имя пользователя, который будет добавлен.
1. Нажмите кнопку **Сохранить**, чтобы добавить пользователя к роли.


> [!IMPORTANT]
> Моментальные снимки могут содержать личные и другие конфиденциальные данные в значениях переменных и параметров.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Отладка моментальных снимков на портале Application Insights

Если для заданного исключения или идентификатора проблемы доступен моментальный снимок, то для этого [исключения](app-insights-asp-net-exceptions.md) на портале Application Insights доступна кнопка **Open Debug Snapshot** (Открыть отладочный моментальный снимок).

![Кнопка "Open Debug Snapshot" (Открыть отладочный моментальный снимок) для исключения](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

В представлении "Debug Snapshot" (Отладочный моментальный снимок) можно увидеть стек вызовов и область переменных. При выборе кадров стека вызовов в области стека вызовов можно просматривать локальные переменные и параметры для этого вызова функции в области переменных.

![Просмотр отладочного моментального снимка на портале](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Моментальные снимки могут содержать конфиденциальные сведения и по умолчанию не отображаются. Для просмотра моментальных снимков вам должна быть назначена роль `Application Insights Snapshot Debugger`.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Отладка моментальных снимков с помощью Visual Studio 2017 Enterprise
1. Нажмите кнопку **Download Snapshot** (Скачать моментальный снимок), чтобы скачать файл с расширением `.diagsession`, который можно открыть в Visual Studio 2017 Enterprise.

2. Чтобы открыть файл с расширением `.diagsession`, сначала нужно [скачать и установить расширение отладчика моментальных снимков для Visual Studio](https://aka.ms/snapshotdebugger).

3. После открытия файла моментального снимка в Visual Studio появится страница мини-дампа отладки. Щелкните **Debug Managed Code** (Отладить управляемый код), чтобы начать отладку моментального снимка. Откроется строка кода, на которой было порождено исключение, и вы сможете выполнить отладку текущего состояния процесса.

    ![Просмотр отладочного моментального снимка в Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Загруженный моментальный снимок содержит все файлы символов, найденные на сервере веб-приложения. Эти файлы символов требуются для связывания данных моментального снимка с исходным кодом. Для приложений службы приложений не забудьте включить развертывание символов при публикации веб-приложения.

## <a name="how-snapshots-work"></a>Как работают моментальные снимки

Сборщик моментальных снимков реализуется в виде [обработчика телеметрии Application Insights](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). При запуске приложения обработчик телеметрии как компонент сборщика моментальных снимков добавляется в конвейер телеметрии вашего приложения.
Каждый раз, когда приложение вызывает [TrackException](app-insights-asp-net-exceptions.md#exceptions), сборщик моментальных снимков вычисляет идентификатор проблемы из типа вызываемого исключения и метод создания исключения.
При этом значение счетчика увеличивается для соответствующего идентификатора проблемы. Когда счетчик достигает значения `ThresholdForSnapshotting`, идентификатор проблемы добавляется в план сбора.

Сборщик моментальных снимков также отслеживает исключения во время их создания, используя подписку на событие [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception). При возникновении этого события идентификатор проблемы исключения вычисляется и сравнивается с идентификаторами в плане сбора.
Если обнаружено соответствие, создается моментальный снимок выполняющегося процесса. Моментальному снимку назначается уникальный идентификатор, и к исключению добавляется метка с этим идентификатором. После возврата ответа обработчиком FirstChanceException вызванное исключение обрабатывается в обычном режиме. Со временем исключение снова достигает метода TrackException. Тогда оно вместе с идентификатором моментального снимка сообщается в Application Insights.

Основной процесс продолжает выполняться и обслуживать трафик для пользователей с небольшим прерыванием. Тем временем моментальный снимок передается в процесс передачи моментальных снимков. Отправитель моментальных снимков создает минидамп и передает его в Application Insights вместе с соответствующими PDB-файлами символов.

> [!TIP]
> - Моментальный снимок процесса — это приостановленный клон выполняющегося процесса.
> - Создание моментального снимка занимает около 10–20 мс.
> - Значение по умолчанию для `ThresholdForSnapshotting` — 1. Это минимальное значение. Таким образом, прежде чем будет создан моментальный снимок, приложение должно вызвать то же исключение **дважды**.
> - Задайте для `IsEnabledInDeveloperMode` значение true, если требуется создавать моментальные снимки во время отладки в Visual Studio.
> - Частота создания моментальных снимков ограничивается параметром `SnapshotsPerTenMinutesLimit`. По умолчанию установлено ограничение — один моментальный снимок каждые десять минут.
> - Можно отправлять не более 50 моментальных снимков в день.

## <a name="current-limitations"></a>Текущие ограничения

### <a name="publish-symbols"></a>Публикация символов
Отладчику моментальных снимков требуется наличие файлов символов на рабочем сервере для декодирования переменных и обеспечения возможности отладки в Visual Studio. Выпуск 15.2 приложения Visual Studio 2017 по умолчанию публикует символы для сборок выпуска при публикации в службе приложений. В предыдущих версиях в профиль публикации `.pubxml` необходимо добавить приведенную ниже строку, чтобы символы публиковались в режиме выпуска.

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Для среды вычислений Azure и других типов сред убедитесь, что файлы символов находятся в той же папке, что и библиотеки DLL основного приложения (обычно это `wwwroot/bin`), или доступны по текущему пути.

### <a name="optimized-builds"></a>Оптимизированные сборки
В некоторых случаях локальные переменные не могут отображаться в сборках выпуска из-за оптимизаций, примененных JIT-компиляторов.
Однако в службах приложений Azure сборщик моментальных снимков может деоптимизировать методы создания исключений, которые являются частью плана сбора.

> [!TIP]
> Установите расширение сайта Application Insights в службе приложений для получения поддержки деоптимизации.

## <a name="troubleshooting"></a>Устранение неполадок

Эти советы помогут при устранении проблем, связанных с отладчиком моментальных снимков.

### <a name="use-the-snapshot-health-check"></a>Использование проверки работоспособности моментальных снимков
Из-за некоторых распространенных проблем окно "Открыть моментальный снимок отладки" может не отображаться. Например, из-за использования устаревшего сборщика моментальных снимков, достижения ежедневного лимита отправки или длительного времени передачи моментального снимка. Для устранения распространенных неполадок можно использовать проверку работоспособности моментальных снимков.

В области исключения в представлении сквозной трассировки есть ссылка, которая позволяет перейти к окну проверки работоспособности моментальных снимков.

![Введите данные для проверки работоспособности моментальных снимков](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

Интерактивный интерфейс, подобный чату, ищет распространенные проблемы и приводит инструкции по их устранению.

![Проверка работоспособности](./media/app-insights-snapshot-debugger/healthcheck.png)

Если это не устранит проблему, воспользуйтесь следующими шагами по устранению неполадок вручную.

### <a name="verify-the-instrumentation-key"></a>Проверка ключа инструментирования

Убедитесь, что в опубликованном приложении используется правильный ключ инструментирования. Как правило, ключ инструментирования считывается из файла ApplicationInsights.config. Убедитесь, что его значение такое же, что и у ключа инструментирования для ресурса Application Insights, который отображается на портале.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Обновление пакета NuGet до последней версии

Используя диспетчер пакетов NuGet в Visual Studio, убедитесь, что установлена последняя версия Microsoft.ApplicationInsights.SnapshotCollector. Заметки о выпуске можно найти на странице https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Проверьте журналы отправителя

После создания моментального снимка на диске создается файл минидампа (.dmp). Этот файл минидампа и любые связанные PDB-файлы создаются в отдельном процессе передачи в хранилище Snapshot Debugger Application Insights. После успешной передачи минидамп удаляется с диска. Файлы журнала для процесса передачи сохраняются на диске. В среде службы приложений эти журналы можно найти в `D:\Home\LogFiles`. Используйте веб-сайт управления Kudu для службы приложений, чтобы найти эти файлы журнала.

1. Откройте службу приложений на портале Azure.
2. Щелкните **Дополнительные инструменты** или выполните поиск по запросу **Kudu**.
3. Щелкните **Переход**.
4. В раскрывающемся списке **Debug console** (Консоль отладки) выберите **CMD** (Команда).
5. Щелкните **LogFiles**.

Вы увидите хотя бы один файл с именем, начинающимся с `Uploader_` или `SnapshotUploader_`, и расширением `.log`. Щелкните соответствующую пиктограмму, чтобы скачать все файлы журналов, или открыть их в браузере.
Имя файла содержит уникальный суффикс, который идентифицирует экземпляр службы приложений. Если экземпляр службы приложений размещен на нескольких компьютерах, для каждого компьютера существуют отдельные файлы журналов. Когда отправитель обнаруживает новый файл минидампа, он записывается в файл журнала. Ниже приведен пример успешного моментального снимка и отправки:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Приведенный выше пример взят из пакета NuGet Microsoft.ApplicationInsights.SnapshotCollector версии 1.2.0. В более ранних версиях процесс передачи называется `MinidumpUploader.exe`, а журнал содержит менее подробные сведения.

В предыдущем примере, ключ инструментирования — это `c12a605e73c44346a984e00000000000`. Это значение должно соответствовать ключу инструментирования для вашего приложения.
Минидамп связан с моментальным снимком с идентификатором `139e411a23934dc0b9ea08a626db16c5`. Позже этот идентификатор можно использовать для поиска связанной телеметрии исключений в аналитике Application Insights.

Отправитель проверяет наличие новых PDB-файлов примерно один раз каждые 15 минут. Ниже приведен пример:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Для приложений, которые _не_ размещаются в службе приложений, журналы отправителя находятся в той же папке минидампов: `%TEMP%\Dumps\<ikey>` (где `<ikey>` ваш ключ инструментирования).

### <a name="troubleshooting-cloud-services"></a>Устранение неполадок с облачными службами
Для ролей в облачных службах размер временной папки по умолчанию может быть слишком мал, чтобы хранить файлы минидампов. Это приводит к потере моментальных снимков.
Необходимый размер зависит от общего рабочего набора приложения и количества параллельных моментальных снимков.
Как правило, рабочий набор веб-роли 32-разрядного приложения ASP.NET может занимать от 200 МБ до 500 МБ.
Предусмотрите место хотя бы для двух параллельных моментальных снимков.
Например, если приложение использует 1 ГБ для всего рабочего набора, следует убедиться, что доступно 2 ГБ дискового пространства для хранения моментальных снимков.
Выполните следующие действия, чтобы настроить роль облачной службы с выделенным локальным ресурсом для моментальных снимков.

1. Добавьте новый локальный ресурс в облачную службу, изменив файл ее определения (.csdef). В следующем примере определяется ресурс с именем `SnapshotStore` и размером 5 ГБ.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Измените код запуска роли, чтобы добавить переменную среды, указывающую на локальный ресурс `SnapshotStore`. Для рабочих ролей код необходимо добавить в метод `OnStart` нужной роли:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Для веб-ролей (ASP.NET) код необходимо добавить в метод `Application_Start` веб-приложения:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Обновите файл ApplicationInsights.config для роли, чтобы переопределить размещение временной папки, которое используется в `SnapshotCollector`.
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Поиск исключений с моментальными снимками с помощью поиска Application Insights

При создании моментального снимка вызванное исключение обозначается идентификатором моментального снимка. При передаче телеметрии исключения в Application Insights этот идентификатор моментального снимка добавляется в качестве пользовательского свойства. В колонке **поиска** Application Insights можно найти все данные телеметрии с пользовательским свойством `ai.snapshot.id`.

1. Перейдите к ресурсу Application Insights на портале Azure.
2. Щелкните **Search**(Поиск).
3. В текстовом поле поиска введите `ai.snapshot.id` и нажмите клавишу ВВОД.

![Поиск телеметрии с помощью идентификатора моментального снимка на портале](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Если этот поиск не дал результатов, значит в Application Insights не передавались моментальные снимки для приложения в выбранный диапазон времени.

Чтобы найти определенный идентификатор снимка из журналов отправителя, введите этот идентификатор в поле поиска. Если не удается найти данные телеметрии для моментального снимка, который был отправлен, выполните следующие действия:

1. Еще раз проверьте, что вы смотрите на правильный ресурс Application Insights при проверке ключа инструментирования.

2. С помощью метки времени из журнала отправителя, настройте фильтр диапазона времени поиска так, чтобы охватить этот диапазон времени.

Если исключение с таким идентификатором моментальных снимков по-прежнему не отображается, значит телеметрия исключения не отправлялась в Application Insights. Это происходит в случае сбоя приложения после снятия снимка, но до того как оно передало телеметрию исключений. В этом случае проверьте журналы службы приложений в `Diagnose and solve problems`, чтобы проверить, были ли неожиданные перезагрузки или необработанные исключения.

### <a name="edit-network-proxy-or-firewall-rules"></a>Изменение правил прокси-сервера сети или брандмауэра

Если приложение подключается к Интернету через прокси-сервер или брандмауэр, может потребоваться изменить правила, чтобы разрешить приложению взаимодействовать со службой Snapshot Debugger. Вот [список IP-адресов и портов, используемых службой Snapshot Debugger](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Дополнительная информация

* [Установите в коде точки прикрепления](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications), чтобы получать моментальные снимки не ожидая исключений.
* В статье [Диагностика исключений в веб-приложениях с помощью Application Insights](app-insights-asp-net-exceptions.md) объясняется, как отобразить дополнительные исключения в Application Insights.
* В статье [Интеллектуальное обнаружение в Application Insights](app-insights-proactive-diagnostics.md) описывается автоматическое обнаружение аномалий производительности.
