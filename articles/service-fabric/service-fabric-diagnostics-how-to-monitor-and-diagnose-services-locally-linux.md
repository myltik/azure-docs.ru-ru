---
title: Отладка микрослужб Azure в Linux | Документация Майкрософт
description: Узнайте, как осуществлять мониторинг и диагностику состояния служб с использованием платформы Microsoft Azure Service Fabric на локальном компьютере для разработки.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 563f9d73d5a8d56e834c36d03aed75812ec123ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212716"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Мониторинг и диагностика состояния служб в локальной среде разработки


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Благодаря возможностям мониторинга состояния, а также выявления, диагностики и устранения неполадок службы могут работать практически без перерывов. Мониторинг и диагностика критически важны в фактически развернутой рабочей среде. Внедрение аналогичной модели при разработке служб гарантирует работу конвейера диагностики при переходе в рабочую среду. Платформа Service Fabric позволяет использовать средства диагностики, которые одинаково хорошо работают как в среде разработки на одном локальном компьютере, так и в условиях реального рабочего кластера.


## <a name="debugging-service-fabric-java-applications"></a>Отладка приложений Java в Service Fabric

Для приложений Java доступно [несколько платформ ведения журналов](http://en.wikipedia.org/wiki/Java_logging_framework) . Так как `java.util.logging` является параметром по умолчанию в среде JRE, он также используется для [примеров кода в GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  Далее в этой статье описывается настройка платформы `java.util.logging` .

С помощью java.util.logging журналы приложения можно перенаправлять в память, потоки вывода, файлы консоли или сокеты. Для каждого из этих вариантов существуют обработчики по умолчанию, входящие в состав платформы. Чтобы настроить обработчик файлов для приложения, который будет перенаправлять все журналы в локальный файл, можно создать файл `app.properties`.

Пример конфигурации приведен в следующем фрагменте кода:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Файл `app.properties` должен указывать на существующую папку. Затем, после создания файла `app.properties`, необходимо также изменить сценарий точки входа `entrypoint.sh` в папке `<applicationfolder>/<servicePkg>/Code/`, указав в качестве значения свойства `java.util.logging.config.file` файл `app.propertes`. Запись должна выглядеть примерно так:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


В результате применения этой конфигурации журналы будут циклически собираться в папку `/tmp/servicefabric/logs/`. В этом случае файл журнала называется mysfapp%u.%g.log, где:
* **%u** — это уникальный номер для разрешения конфликтов между параллельными процессами Java.
* **%g** — это номер версии для отличия чередующихся журналов.

Если обработчик не настроен явно, по умолчанию регистрируется обработчик консоли. Просмотреть журналы в системном журнале можно в папке /var/log/syslog.

Дополнительные сведения см. на странице с [примерами кода на GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  


## <a name="debugging-service-fabric-c-applications"></a>Отладка приложений C# в Service Fabric


Для трассировки приложений CoreCLR на платформе Linux доступно несколько платформ. Дополнительные сведения см. в разделе о [ведении журналов](http:/github.com/aspnet/logging) на сайте GitHub.  Так как EventSource знаком разработчикам на языке C#, в этой статье он используется для трассировки в образцах CoreCLR на Linux.

Сначала необходимо добавить System.Diagnostics.Tracing, чтобы иметь возможность записывать журналы в память, выходные потоки или файлы консоли.  Для ведения журналов с помощью EventSource добавьте в project.json следующий проект:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Можно использовать пользовательский EventListener, чтобы прослушивать события службы и соответствующим образом перенаправлять их в файлы трассировки. В следующем фрагменте кода показан пример реализации ведения журналов с помощью EventSource и пользовательского EventListener.


```csharp

 public class ServiceEventSource : EventSource
 {
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
   internal class ServiceEventListener : EventListener
   {

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
            using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))           
        { 
                 // report all event information               
         Out.Write(" {0} ",  Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                if (eventData.Message != null)              
            Out.WriteLine(eventData.Message, eventData.Payload.ToArray());              
            else             
        { 
                    string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                    Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");             
        }
           }
        }
    }
```


Предыдущий фрагмент кода выводит журналы в файл в `/tmp/MyServiceLog.txt`. Это имя файла должно быть обновлено соответствующим образом. Если вы хотите перенаправить журналы в консоль, используйте следующий фрагмент кода в классе настраиваемого EventListener:

```csharp
public static TextWriter Out = Console.Out;
```

Образцы на языке C# [в репозитории GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) регистрируют события в файл с помощью EventSource и пользовательского EventListener.



## <a name="next-steps"></a>Дополнительная информация
Код трассировки, добавленный в приложение, также можно использовать для диагностики приложения в кластере Azure. Ознакомьтесь с этими статьями, в которых рассматриваются различные варианты инструментов и описывается, как их настроить.
* [Сбор журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-lad.md)
