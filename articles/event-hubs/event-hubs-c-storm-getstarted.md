<properties
	pageTitle="Приступая к работе с концентраторами событий"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события с помощью C, и их получению в кластере Apache Storm."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article" 
	ms.date="09/01/2015"
	ms.author="sethm"/>

# Приступая к работе с концентраторами событий

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Введение

Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в статье [Обзор концентраторов событий].

В этом учебнике вы узнаете, как принимать сообщения в концентратор событий с помощью консольного приложения на языке C и извлекать их параллельно с использованием Apache Storm.

Для работы с этим учебником необходимо следующее.

+ Среда разработки C. В этом учебнике предполагается, что применяется стек gcc на [виртуальной машине Azure Linux](../virtual-machines/virtual-machines-linux-tutorial.md) с Ubuntu 14.04. Инструкции для других сред будут предоставлены как внешние ссылки.

+ Среда разработки Java, настроенная для запуска [Maven](http://maven.apache.org/). Для этого учебника предполагается использование среды [Eclipse](https://www.eclipse.org/).

+ Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

## Создание концентратора событий

1. Войдите на [портал Azure] и нажмите **СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните **Службы приложений**, **Служебная шина**, **Концентратор событий** и **Быстрое создание**.

	![][1]

3. Введите имя для концентратора событий, выберите нужный регион и щелкните **Создать новый концентратор событий**.

	![][2]

4. Щелкните ранее созданное пространство имен (обычно это ***имя концентратора событий*-ns**).

	![][3]

5. Щелкните расположенную в верхней части страницы вкладку **Концентраторы событий** и выберите недавно созданный концентратор событий.

	![][4]

6. Щелкните вкладку **Настройка** в верхней части страницы, добавьте правило **SendRule** с правами *Отправка*, добавьте другое правило с именем **ReceiveRule** с правами *Прослушивание*, а затем нажмите кнопку **Сохранить**.

	![][5]

7. На той же странице запишите созданные ключи для **SendRule** и **ReceiveRule**.

	![][6c]

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Запуск приложений

Теперь все готово к запуску приложений.

1.	Запустите класс **LogTopology** в Eclipse, а затем подождите, пока будут запущены приемники для всех разделов.

2.	Запустите программу **sender**, после чего в окне приемника появятся события.

	![][23]

> [AZURE.NOTE]В этом учебнике для целей разработки используйте Storm только в локальном режиме. Подробнее о развертываниях и шаблонах Storm см. в разделе [Обзор HDInsight Storm], а также в официальной документации по [Apache Storm].

## Дальнейшие действия

Для разработки приложений с интеграцией концентраторов событий и Storm доступны следующие ресурсы.

- [Анализ данных датчиков в Storm и HDInsight] — это полный учебник, в котором концентраторы событий, Storm и HBase используются для приема данных датчиков в кластере Hadoop.
- [Разработка приложений для обработки потоковых данных с помощью SCP.NET и C# на основе Storm и HDInsight] — это учебник по написанию конвейеров Storm на C#.

<!-- Images. -->
[1]: ./media/event-hubs-c-storm-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-c-storm-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-c-storm-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-c-storm-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-c-storm-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/event-hubs-c-storm-getstarted/create-event-hub6c.png

[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[портал Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Обзор HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md/
[Анализ данных датчиков в Storm и HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Разработка приложений для обработки потоковых данных с помощью SCP.NET и C# на основе Storm и HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 

<!---HONumber=Oct15_HO3-->