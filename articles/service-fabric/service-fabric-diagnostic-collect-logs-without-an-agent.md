---
title: "Сбор журналов непосредственно из процесса службы Azure Service Fabric | Microsoft Azure"
description: "Описывает, как приложения Service Fabric могут отправлять журналы непосредственно в центральное расположение, например Azure Application Insights или Elasticsearch, не полагаясь на агент системы диагностики Azure."
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz
translationtype: Human Translation
ms.sourcegitcommit: d7f7b157d8d6fb54259c8f23d5005509f4eb7872
ms.openlocfilehash: 22acb6afbfbfff753e71b5e821385798cc76ffdd
ms.lasthandoff: 01/19/2017


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>Сбор журналов непосредственно из процесса службы Azure Service Fabric
## <a name="in-process-log-collection"></a>Внутрипроцессный сбор журналов
Сбор журналов приложений с помощью [расширения системы диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md) — хороший вариант для служб **Azure Service Fabric**, если набор источников и мест назначения журналов невелик и редко меняется, а источники и места назначения легко сопоставимы. В противном случае можно настроить службы для отправки своих журналов в центральное расположение. Этот процесс называется **внутрипроцессным сбором журналов** и имеет несколько потенциальных преимуществ.

* *Простая настройка и развертывание.*

    * Конфигурация сбора диагностических данных является лишь частью конфигурации службы. Ее легко сохранять "синхронизированной" с остальной частью приложения.
    * Настройка каждого приложения или службы также является несложной задачей.
        * Сбор журналов на основе агента обычно требует отдельного развертывания и настройки агента диагностики. Как правило, это прибавляет работы администратору и может являться источником ошибок. Часто на виртуальной машине (узле) существует только один экземпляр агента, и конфигурация агента совместно используется всеми приложениями и службами, запущенными на этом узле. 

* *Гибкость*
   
    * Приложение может отправлять данные куда угодно при наличии клиентской библиотеки, поддерживающей систему хранения целевых данных. При необходимости можно добавлять новые расположения.
    * Можно реализовать сложные правила сбора, фильтрации и статистической обработки данных.
    * Сбор журналов на основе агента часто ограничивается приемниками данных, которые поддерживает агент. Некоторые агенты являются расширяемыми.

* *Доступ к внутренним данным приложения и контексту*
   
    * В подсистему диагностики, которая работает внутри процесса приложения или службы, можно легко добавить трассировку с помощью контекстной информации.
    * В случае сбора журналов на основе агента данные должны отправляться в агент с помощью механизма межпроцессного взаимодействия, например трассировки событий Windows. Однако при таком механизме могут возникнуть дополнительные ограничения.

Преимущества обоих методов сбора можно объединить. Это действительно может оказаться наилучшим решением для многих приложений. Сбор на основе агента — логичное решение для сбора журналов, связанных со всем кластером и отдельными его узлами. Он гораздо надежнее, чем внутрипроцессный сбор журналов, и лучше подходит для диагностики проблем и сбоев при запуске службы. Кроме того, если в кластере Service Fabric запущено много служб, каждая из них выполняет собственный внутрипроцессный сбор журналов, что приведет к многочисленным исходящим подключениям из кластера. Большое количество исходящих подключений нагружает как подсистему сети, так и место назначения журналов, что увеличивает затраты. Агент (например, агент [**системы диагностики Azure**](../cloud-services/cloud-services-dotnet-diagnostics.md)) может собирать данные от нескольких служб и отправлять все данные через небольшое число подключений, повышая производительность. 

В этой статье показано, как настроить внутрипроцессный сбор журналов с помощью [**библиотеки с открытым кодом EventFlow**](https://github.com/Azure/diagnostics-eventflow). Для этой цели можно использовать и другие библиотеки, но EventFlow имеет преимущество, так как она была разработана специально для внутрипроцессного сбора журналов и поддержки служб Service Fabric. Мы используем [**Azure Application Insights**](https://azure.microsoft.com/services/application-insights/) в качестве места назначения журналов. Можно также использовать [**концентраторы событий**](https://azure.microsoft.com/services/event-hubs/) или [**Elasticsearch**](https://www.elastic.co/products/elasticsearch). Это лишь вопрос установки соответствующего пакета NuGet и настройки назначения в файле конфигурации EventFlow. Дополнительные сведения о местах назначения журналов, отличных от Application Insights, см. в [документации по EventFlow](https://github.com/Azure/diagnostics-eventflow).

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>Добавление библиотеки EventFlow в проект службы Service Fabric
Двоичные файлы EventFlow предоставляются как набор пакетов NuGet. Чтобы добавить библиотеку EventFlow в проект службы Service Fabric, щелкните его правой кнопкой мыши в обозревателе решений и выберите "Управление пакетами NuGet". Перейдите на вкладку "Обзор" и найдите `Diagnostics.EventFlow`.

![Пакеты NuGet EventFlow в диспетчере пакетов NuGet Visual Studio][1]

Служба, в которой размещается EventFlow, должна содержать соответствующие пакеты в зависимости от источника и назначения журналов приложений. Добавьте приведенные ниже пакеты. 

* `Microsoft.Diagnostics.EventFlow.Input.EventSource` 
    * (Для сбора данных из класса EventSource службы и стандартных классов EventSource, например *Microsoft-ServiceFabric-Services* и *Microsoft-ServiceFabric-Actors*.)
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` 
    * (Мы собираемся отправлять журналы в ресурс Azure Application Insights.)  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * (Позволяет инициализировать конвейер EventFlow из конфигурации службы Service Fabric и сообщать о всех проблемах отправки диагностических данных в виде отчетов о работоспособности Service Fabric.)

> [!NOTE]
> Для пакета `Microsoft.Diagnostics.EventFlow.Input.EventSource` требуется, чтобы в проекте службы использовалась платформа .NET Framework 4.6 или более поздняя версия. Обязательно задайте соответствующую целевую платформу в свойствах проекта, прежде чем устанавливать этот пакет. 

После установки всех пакетов следующим шагом является настройка и включение EventFlow в службе.

## <a name="configuring-and-enabling-log-collection"></a>Настройка и включение сбора журналов
Конвейер EventFlow, отвечающий за отправку журналов, создается на основе спецификации, хранящейся в файле конфигурации. Пакет `Microsoft.Diagnostics.EventFlow.ServiceFabric` устанавливает начальный файл конфигурации EventFlow в паку решения `PackageRoot\Config`. Это файл `eventFlowConfig.json`. Данный файл конфигурации нужно изменить, чтобы собирать данные из класса службы по умолчанию `EventSource` и отправлять их в службу Application Insights.

> [!NOTE]
> Мы предполагаем, что вы уже работали со службой **Azure Application Insights** и у вас есть ресурс Application Insights, который планируется использовать для мониторинга службы Service Fabric. Если вам требуются дополнительные сведения, ознакомьтесь с разделом [Создание ресурса Application Insights](../application-insights/app-insights-create-new-resource.md).

Откройте файл `eventFlowConfig.json` в редакторе и измените его содержимое, как показано ниже. Обязательно замените имя ServiceEventSource и ключ инструментирования Application Insights в соответствии с комментариями. 

```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

> [!NOTE]
> Имя ServiceEventSource службы — это значение свойства Name в `EventSourceAttribute`, которое применяется к классу ServiceEventSource. Все это указывается в файле `ServiceEventSource.cs`, который является частью кода службы. Например, в приведенном ниже фрагменте кода именем ServiceEventSource является *MyCompany Application1-Stateless1*.
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

Обратите внимание, что файл `eventFlowConfig.json` входит в пакет конфигурации службы. Изменения в этот файл могут вноситься только полными обновлениями или обновлениями конфигурации службы, которые проходят проверку работоспособности обновлений Service Fabric и автоматически откатываются в случае сбоя. Дополнительные сведения см. в разделе [Обновление приложения Service Fabric](service-fabric-application-upgrade.md).

Завершающим шагом является создание экземпляра конвейера EventFlow в коде запуска службы, расположенном в файле `Program.cs`. В следующем примере изменения, связанные с EventFlow, помечены комментариями, которые начинаются с `****`.

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    
                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Имя, переданное в качестве параметра в метод `CreatePipeline` класса `ServiceFabricDiagnosticsPipelineFactory`, — это имя *сущности работоспособности*, представляющей конвейер EventFlow для сбора журналов. Это имя используется в том случае, если EventFlow обнаруживает ошибку и сообщает о ней через подсистему работоспособности Service Fabric.

## <a name="verification"></a>Проверка
Запустите службу и просмотрите окно выходных данных отладки в Visual Studio. После запуска службы вы должны увидеть подтверждения того, что она отправляет записи телеметрии Application Insights. Откройте веб-браузер и перейдите к своему ресурсу Application Insights. Откройте вкладку "Поиск" (в верхней части колонки "Обзор", используемой по умолчанию). После небольшой задержки ваши трассировки должны начать появляться на портале Application Insights.

![Журналы из Service Fabric на портале Application Insights][2]

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения о диагностике и мониторинге службы Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [Документация по EventFlow](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png
