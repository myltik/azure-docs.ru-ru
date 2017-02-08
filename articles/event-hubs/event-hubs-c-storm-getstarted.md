---
title: "Приступая к работе с концентраторами событий в C и Apache Storm | Документация Майкрософт"
description: "Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события с помощью C, и их получению в кластере Apache Storm."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 3b15825e-c53c-471b-870c-686bff46885a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: java
ms.topic: article
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 33a960ae39ab8fa89126201fd67f13e5536413ef
ms.openlocfilehash: 7bad4df26d10ba8edae311b41d0c07be2db374f4


---
# <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Введение
Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в разделе [Общие сведения о концентраторах событий].

В этом учебнике вы узнаете, как принимать сообщения в концентратор событий с помощью консольного приложения на языке C и извлекать их параллельно с использованием Apache Storm.

Для работы с этим учебником необходимо следующее.

* Среда разработки C. В этом учебнике предполагается, что применяется стек gcc на [виртуальной машине Azure Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с Ubuntu 14.04. Инструкции для других сред будут предоставлены как внешние ссылки.
* Среда разработки Java, настроенная для запуска [Maven](http://maven.apache.org/). Для этого учебника предполагается использование среды [Eclipse](https://www.eclipse.org/).
* Активная учетная запись Azure. Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. Запустите класс **LogTopology** в Eclipse, а затем подождите, пока будут запущены приемники для всех разделов.
2. Запустите программу **sender** , после чего в окне приемника появятся события.
   
   ![][23]

> [!NOTE]
> В этом учебнике для целей разработки используйте Storm только в локальном режиме. Дополнительные сведения о развертываниях и шаблонах Storm см. в разделе [Обзор HDInsight Storm], а также в официальной документации по [Apache Storm].

## <a name="next-steps"></a>Дальнейшие действия
Для разработки приложений с интеграцией концентраторов событий и Storm доступны следующие ресурсы.

* [Анализ данных датчиков в Storm и HDInsight][Analyzing sensor data with Storm and HDInsight] — это полное руководство, в котором концентраторы событий, Storm и HBase используются для приема данных датчиков в кластере Hadoop.
* [Разработка приложений для обработки потоковых данных с помощью SCP.NET и C# на основе Storm и HDInsight][Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight] — это руководство по созданию конвейеров Storm на C#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Общие сведения о концентраторах событий]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Обзор HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md
[Analyzing sensor data with Storm and HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Dec16_HO1-->


