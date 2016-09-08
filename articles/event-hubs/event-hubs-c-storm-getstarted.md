<properties
	pageTitle="Приступая к работе с концентраторами событий в C и Apache Storm | Microsoft Azure"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события с помощью C, и их получению в кластере Apache Storm."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="sethm"/>

# Приступая к работе с концентраторами событий

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Введение

Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в статье [Обзор концентраторов событий].

В этом учебнике вы узнаете, как принимать сообщения в концентратор событий с помощью консольного приложения на языке C и извлекать их параллельно с использованием Apache Storm.

Для работы с этим учебником необходимо следующее.

+ Среда разработки C. В этом учебнике предполагается, что применяется стек gcc на [виртуальной машине Azure Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md) с Ubuntu 14.04. Инструкции для других сред будут предоставлены как внешние ссылки.

+ Среда разработки Java, настроенная для запуска [Maven](http://maven.apache.org/). Для этого учебника предполагается использование среды [Eclipse](https://www.eclipse.org/).

+ Активная учетная запись Azure. Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Запуск приложений

Теперь все готово к запуску приложений.

1.	Запустите класс **LogTopology** в Eclipse, а затем подождите, пока будут запущены приемники для всех разделов.

2.	Запустите программу **sender**, после чего в окне приемника появятся события.

	![][23]

> [AZURE.NOTE] В этом учебнике для целей разработки используйте Storm только в локальном режиме. Подробнее о развертываниях и шаблонах Storm см. в разделе [Обзор HDInsight Storm], а также в официальной документации по [Apache Storm].

## Дальнейшие действия

Для разработки приложений с интеграцией концентраторов событий и Storm доступны следующие ресурсы.

- [Анализ данных датчиков в Storm и HDInsight][] — это полный учебник, в котором концентраторы событий, Storm и HBase используются для приема данных датчиков в кластере Hadoop.
- [Разработка приложений для обработки потоковых данных с помощью SCP.NET и C# на основе Storm и HDInsight][] — это учебник по написанию конвейеров Storm на C#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Обзор HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md/
[Анализ данных датчиков в Storm и HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Разработка приложений для обработки потоковых данных с помощью SCP.NET и C# на основе Storm и HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md

<!---HONumber=AcomDC_0817_2016-->
