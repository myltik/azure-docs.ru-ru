---
title: "Приступая к работе с концентраторами событий в Java и Apache Storm | Документация Майкрософт"
description: "Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события с помощью Java, и их получению в кластере Apache Storm."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 385869bd-1ebe-44ae-8113-cc4679a568eb
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 52c9ded8af99c5ae0836be8e71394dd1e5072aff
ms.openlocfilehash: 7566a8d0643607ee80c056ed4410aefd930226b8


---
# <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Введение
Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в статье [Обзор концентраторов событий][Event Hubs Overview].

В этом руководстве описано, как принимать сообщения в концентраторе событий с помощью консольного приложения на языке Java и как извлекать их параллельно с использованием Apache Storm.

Для работы с этим учебником необходимо следующее.

* Среда разработки Java, настроенная для запуска [Maven](http://maven.apache.org/). Для этого руководства предполагается использование среды [Eclipse](https://www.eclipse.org/).
* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. Запустите класс **LogTopology** в Eclipse, а затем подождите, пока будут запущены приемники для всех разделов.
2. Запустите проект **Sender** и нажмите в окне консоли клавишу **ВВОД**. После этого в окне приемника начнут отображаться события.
   
    ![][22]

> [!NOTE]
> В этом учебнике для целей разработки используйте Storm только в локальном режиме. Дополнительные сведения о развертываниях и шаблонах Storm см. в разделе [Обзор HDInsight Storm][HDInsight Storm Overview] и официальной документации по [Apache Storm][Apache Storm].
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Для разработки приложений с интеграцией концентраторов событий и Storm доступны следующие ресурсы.

* [Анализ данных датчиков в Storm и HDInsight] — это полное руководство, в котором концентраторы событий, Storm и HBase используются для приема данных датчиков в кластере Hadoop.
* [Разработка приложений для обработки потоковых данных с помощью SCP.NET и C# на основе Storm и HDInsight][Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight] — это руководство по созданию конвейеров Storm на C#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs Overview]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm Overview]: ../hdinsight/hdinsight-storm-overview.md
[Анализ данных датчиков в Storm и HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Dec16_HO2-->


