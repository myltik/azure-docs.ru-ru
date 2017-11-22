---
title: "Счетчики производительности в Application Insights | Документация Майкрософт"
description: "Мониторинг системных и пользовательских счетчиков производительности .NET в Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: mbullwin
ms.openlocfilehash: 40821d32c5bdfe51bb3cb205660d6f25b2c3fadc
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2017
---
# <a name="system-performance-counters-in-application-insights"></a>Системные счетчики производительности в Application Insights
В Windows предусмотрены самые разные [счетчики производительности](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters), которые отображают показатели использования ЦП, памяти, диска и сети. Также вы можете определить собственные счетчики. [Application Insights](app-insights-overview.md) отображает эти счетчики производительности, если приложение запущено под управлением службы IIS на локальном узле или виртуальной машине, к которой у вас есть доступ с правами администратора. Диаграммы показывают, какие ресурсы доступны для запущенного приложения, а также позволяют обнаружить неравномерность в загрузке экземпляров сервера.

Счетчики производительности отображаются в колонке "Серверы", где представлена таблица с разбивкой по экземплярам сервера.

![Счетчики производительности, отображаемые в Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Счетчики производительности недоступны для веб-приложений Azure. Но зато вы можете [отправить данные системы диагностики Azure в Application Insights](app-insights-azure-diagnostics.md).)

## <a name="view-counters"></a>Просмотр счетчиков
В колонке "Серверы" показывается стандартный набор счетчиков производительности. 

Чтобы увидеть другие счетчики, измените диаграммы в колонке "Серверы" или откройте новую колонку [Обозревателя метрик](app-insights-metrics-explorer.md) и добавьте в нее новые диаграммы. 

При изменении диаграммы все доступные счетчики указываются в разделе метрик.

![Счетчики производительности, отображаемые в Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Чтобы собрать в одном месте все важнейшие диаграммы, создайте [панель мониторинга](app-insights-dashboards.md) и закрепите на ней нужные диаграммы.

## <a name="add-counters"></a>Добавление счетчиков
Если нужный счетчик производительности не отображается в списке метрик, значит пакет SDK Application Insights не собирает сведения о нем на веб-сервере. Вы можете изменить настройки, чтобы разрешить сбор данных.

1. Чтобы получить список доступных счетчиков на сервер, выполните на сервере такую команду PowerShell:
   
    `Get-Counter -ListSet *`
   
    (См. [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Откройте файл ApplicationInsights.config.
   
   * Если вы добавили Application Insights в приложение во время разработки, отредактируйте файл ApplicationInsights.config в проекте и повторно разверните его на серверах.
   * Если монитор состояний использовался как инструментарий для веб-приложения во время выполнения, файл ApplicationInsights.config вы найдете в корневом каталоге приложения в IIS. Обновите его в этом расположении на каждом экземпляре сервера.
3. Измените директиву сборщика данных производительности:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Вы можете собирать показания как стандартных счетчиков, так и созданных вами самостоятельно. Например, стандартный счетчик `\Objects\Processes` доступен во всех системах Windows. Пример пользовательского счетчика, который можно реализовать в веб-службе: `\Sales(photo)\# Items Sold`. 

Используется формат `\Category(instance)\Counter"`, а для категорий без экземпляров — просто `\Category\Counter`.

`ReportAs` — обязательный параметр для счетчиков, имена которых не соответствуют маске `[a-zA-Z()/-_ \.]+`, т. е. содержат любые символы, не входящие в этот стандартный набор: буквы, круглые скобки, косую черту, дефис, символ подчеркивания, пробел и точку.

При указании экземпляра он будет собираться в качестве измерения CounterInstanceName обнаруженной метрики.

### <a name="collecting-performance-counters-in-code"></a>Сбор данных счетчиков производительности в коде
Чтобы собрать данные счетчиков производительности системы и передать их в Application Insights, можно использовать следующий фрагмент кода:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
То же самое можно сделать с пользовательскими метриками, созданными вами:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Счетчики производительности в службе аналитики
В [службе аналитики](app-insights-analytics.md) можно выполнять поиск отчетов по счетчикам производительности и отображать их.

Схема **PerformanceCounters** предоставляет `category`, имя `counter` и имя `instance` каждого счетчика производительности.  В данных телеметрии для каждого приложения вы увидите только счетчики для этого приложения. Например, вот как можно увидеть, какие счетчики доступны. 

![Счетчики производительности в аналитике Application Insights](./media/app-insights-performance-counters/analytics-performance-counters.png)

(Экземпляр здесь обозначает экземпляр счетчика производительности, а не экземпляр роли или сервера. Имя экземпляра счетчика производительности обычно разделяет такие счетчики, как загруженность процессора, по именам процессов или приложений.)

Вот как можно получить диаграмму доступной памяти за последний период. 

![Временная диаграмма памяти в аналитике Application Insights](./media/app-insights-performance-counters/analytics-available-memory.png)

Как и другие данные телеметрии, данные **performanceCounters** также содержат столбец `cloud_RoleInstance`, который определяет экземпляр сервера, на котором выполняется приложение. Например, вот как можно сравнить производительность приложения на разных компьютерах. 

![Производительность, сегментированная по экземпляру роли в аналитике Application Insights](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET и счетчики Application Insights
*Чем отличаются метрики "Частота исключений" и "Исключения"?*

* *Частота исключений* — это системный счетчик производительности. Среда CLR подсчитывает все обработанные и необработанные исключения и делит их общее количество в интервале выборки на продолжительность интервала. Пакет SDK Application Insights получает этот результат и отправляет его на портал.
* *Исключения* — это количество отчетов TrackException, полученных порталом в интервале выборки диаграммы. Включает только обработанные исключения, в коде которых прописаны вызовы TrackException, и не включает [необработанные исключения](app-insights-asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Счетчики производительности в приложениях ASP.NET Core
Счетчики производительности поддерживаются, только если приложение предназначено для полной версии платформы .NET Framework. Невозможно выполнять сбор данных счетчиков производительности для приложений .Net Core.

## <a name="alerts"></a>Оповещения
Как и для других метрик, вы можете [установить оповещение](app-insights-alerts.md), которое предупредит о выходе показаний счетчика производительности за установленные пределы. Откройте колонку "Оповещения" и щелкните "Добавить оповещение".

## <a name="next"></a>Дальнейшие действия
* [Отслеживание зависимостей](app-insights-asp-net-dependencies.md)
* [Отслеживание исключений](app-insights-asp-net-exceptions.md)

