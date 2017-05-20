---
title: "Отладчик моментальных снимков Azure Application Insights для приложений .NET | Документация Майкрософт"
description: "Отладочные моментальные снимки автоматически собираются при порождении исключений в рабочих приложениях .NET."
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Отладочные моментальные снимки для исключений в приложениях .NET

*Эта функция предоставляется в предварительной версии.*

Можно автоматически собирать отладочный моментальный снимок работающего веб-приложения при порождении исключения. Моментальный снимок отображает состояние исходного кода и переменных в момент порождения этого исключения. Отладчик моментальных снимков в [Application Insights](app-insights-overview.md) отслеживает телеметрию исключений, поступающую из веб-приложения. Он собирает моментальные снимки для наиболее частых исключений, чтобы предоставить вам необходимые сведения для диагностики проблем в рабочей среде. Добавьте [пакет NuGet сборщика моментальных снимков](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) в приложение и, при необходимости, настройте параметры сбора в файле [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Моментальные снимки для [исключений](app-insights-asp-net-exceptions.md) отображаются на портале Application Insights.

Вы можете просмотреть отладочные моментальные снимки на портале, чтобы изучить стек вызовов и проверить значения переменных в каждом кадре стека вызовов. Чтобы воспользоваться более мощными средствами отладки исходного кода, откройте моментальные снимки в Visual Studio 2017 Enterprise, [скачав расширение отладчика моментальных снимков для Visual Studio](https://aka.ms/snapshotdebugger).

Сбор моментальных снимков доступен для веб-приложений ASP.NET, выполняемых на платформе .NET Framework 4.6 или более поздней версии, которые размещены в IIS в службе вычислений Azure или службе приложений Azure.

## <a name="configure-snapshot-collection"></a>Настройка сбора моментальных снимков

1. Если вы еще не сделали это, [включите Application Insights в веб-приложении](app-insights-asp-net.md).

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector). 

3. Просмотрите параметры по умолчанию, добавленные этим пакетом в файл [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

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

Моментальные снимки собираются только для исключений, которые видимы для пакета SDK для Application Insights. В некоторых случаях может потребоваться [настроить сбор исключений](app-insights-asp-net-exceptions.md#exceptions), чтобы просматривать исключения с помощью моментальных снимков, которые отображаются на портале.

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>Отладка моментальных снимков на портале Application Insights

Если для заданного исключения или идентификатора проблемы доступен моментальный снимок, то для этого [исключения](app-insights-asp-net-exceptions.md) на портале Application Insights доступна ссылка *Open Debug Snapshot* (Открыть отладочный моментальный снимок).

![Кнопка "Open Debug Snapshot" (Открыть отладочный моментальный снимок) для исключения](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

В представлении "Debug Snapshot" (Отладочный моментальный снимок) можно увидеть стек вызовов и область переменных. Выбирая кадры стека вызовов в области стека вызовов, можно просматривать локальные переменные и параметры для этого вызова функции в области переменных.

![Просмотр отладочного моментального снимка на портале](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Моментальные снимки могут содержать конфиденциальные сведения и по умолчанию не отображаются. Чтобы просмотреть моментальные снимки, необходимо, чтобы на портале вам была назначена роль `Application Insights Snapshot Debugger` для подписки или ресурса. В настоящее время назначать эту роль владельцы подписок могут только отдельным пользователям. Назначение этой роли группам Azure Active Directory в настоящее время не поддерживается.

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>Отладка моментальных снимков с помощью Visual Studio 2017 Enterprise
Можно нажать кнопку *Download Snapshot* (Скачать моментальный снимок), чтобы скачать файл с расширением `.diagsession`, который можно открыть в Visual Studio 2017 Enterprise. Чтобы открыть файл с расширением `.diagsession`, в настоящее время нужно сначала [скачать и установить расширения отладчика моментальных снимков для Visual Studio](https://aka.ms/snapshotdebugger).

После открытия файла моментального снимка вы перейдете на страницу отладки минидампа Visual Studio, где сможете начать отладку моментального снимка, щелкнув *Debug Managed Code* (Отладка управляемого кода). Вы перейдете к строке кода, на которой было порождено исключение, и сможете выполнить отладку текущего состояния процесса.

![Просмотр отладочного моментального снимка в Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Скачанный моментальный снимок содержит все файлы символов, найденные на сервере веб-приложения. Эти файлы символов требуются для связывания данных моментального снимка с исходным кодом. Для приложений службы приложений Azure обязательно включите развертывание символов при публикации веб-приложений.

## <a name="how-snapshots-work"></a>Как работают моментальные снимки

При запуске приложения создается отдельный процесс передачи моментальных снимков, отслеживающий запросы на создание моментальных снимков для вашего приложения. При запросе создания моментального снимка за 10–20 мс создается теневая копия выполняющегося процесса. Затем теневой процесс анализируется и создается моментальный снимок. При этом основной процесс продолжает работать и обслуживать трафик для пользователей. Моментальный снимок передается в Application Insights вместе с соответствующими PDB-файлами символов, необходимыми для просмотра моментального снимка.

## <a name="current-limitations"></a>Текущие ограничения

### <a name="publishing-symbols"></a>Публикация символов
Отладчику моментальных снимков требуется наличие файлов символов на рабочем сервере для декодирования переменных и обеспечения возможности отладки в Visual Studio. Выпуск 15.2 приложения Visual Studio 2017 по умолчанию публикует символы для сборок выпуска при публикации в службе приложений Azure. В предыдущих версиях в профиль публикации `.pubxml` необходимо добавить приведенную ниже строку, чтобы символы публиковались в режиме выпуска.

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Для среды вычислений Azure и других типов сред убедитесь, что файлы символов находятся в той же папке, что и библиотеки DLL основного приложения (обычно это `wwwroot/bin`), или доступны по текущему пути.

### <a name="optimized-builds"></a>Оптимизированные сборки
В некоторых случаях локальные переменные не отображаются в сборках выпуска из-за оптимизаций, примененных во время процесса сборки. Это ограничение будет исправлено в следующем выпуске пакета NuGet.

## <a name="next-steps"></a>Дальнейшие действия

* В статье [Диагностика исключений в веб-приложениях с помощью Application Insights](app-insights-asp-net-exceptions.md) объясняется, как отобразить дополнительные исключения в Application Insights. 
* В статье [Интеллектуальное обнаружение в Application Insights](app-insights-proactive-diagnostics.md) описывается автоматическое обнаружение аномалий производительности.

