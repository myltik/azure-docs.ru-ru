<properties pageTitle="Приступая к работе с концентраторами событий" metaKeywords ="служебная шина Azure, концентратор событий, приступая к работе с концентраторами событий" description="Follow this tutorial to get started using Azure Event Hubs sending events with Java and receiving them in an Apache Storm cluster" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="java" ms.devlang="java" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Приступая к работе с концентраторами событий

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Концентраторы событий - масштабируемая система потребления, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени аналитики или в кластере хранилища.

Дополнительную информацию см. в разделе [Общие сведения о концентраторах событий].

В этом учебнике вы узнаете, как принимать сообщения в концентратор событий с помощью консольного приложения на языке Java и как извлекать их при параллельно с использованием Apache Storm.

Для работы с этим учебником необходимо следующее.

+ Среда разработки Java, настроенная для запуска [Maven](http://maven.apache.org/). В этом учебнике предполагается, что используется [Eclipse](https://www.eclipse.org/).

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

## Создание концентратора событий

1. Войдите на [портал управления Azure] и щелкните элемент **СОЗДАТЬ** в нижней части экрана.

2.  Последовательно щелкните элементы **Службы приложений**, **Служебная шина**, **Концентратор событий** и **Быстрое создание**.

   	![][1]

3. Введите имя для концентратора событий, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][2]

4. Щелкните только что созданное пространство имен (обычно это ***имя_концентратора_событий*-ns**).

   	![][3]

5. Откройте расположенную сверху вкладку **Концентраторы событий** и выберите недавно созданный концентратор.

   	![][4]

6. Щелкните вкладку **Настройка** в верхней части страницы, добавьте правило **SendRule** с правами *Send*, добавьте другое правило с именем **ReceiveRule** с правами *Listen*, а затем нажмите кнопку **Сохранить**.

   	![][5]

7. На той же странице запишите созданные ключи для **SendRule** и **ReceiveRule**.

   	![][6c]

После создания концентратора событий и необходимо получить строки подключения для отправки и получения событий.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-java](../includes/service-bus-event-hubs-get-started-send-java.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Запуск приложений

Теперь все готово для запуска приложений.

1.	Запустите класс **LogTopology** в Eclipse, а затем подождите, пока будут запущены получатели для всех разделов.

2.	Запустите проект **Sender**, нажмите клавишу **Ввод** в окне консоли, после чего в окне приемника событий появятся события.

   	![][22]

> [AZURE.NOTE] В этом учебнике для целей разработки используйте Storm только в локальном режиме. Дополнительные сведения о развертываниях и шаблонах Storm см. в разделе [Обзор HDInsight] и официальной документации [Apache Storm].

## Дальнейшие действия
Для разработки приложений с интеграцией концентраторов событий и Storm доступны следующие ресурсы.

- [Анализ данных датчиков в Storm и HDInsight] - это полный учебник, в котором концентраторы событий, Storm и HBase используются для приема данных датчиков в кластере Hadoop.
- [Разработка приложений для обработки потоковых данных с помощью SCP.NET и C# на основе Storm и HDInsight] - это учебник по написанию конвейеров Storm на C#.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-getstarted/receive-storm2.png

<!-- Links -->
[Портал управления Azure]: https://manage.windowsazure.com/
[Узел обработчика событий]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: http://msdn.microsoft.com/ru-ru/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Обзор HDinsight Storm]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-storm-overview/
[Анализируя данные от датчиков со Storm и HDInsight]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Разработка приложений для обработки потоковой передачи данных с помощью SCP.NET и C# на Storm и HDInsight]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
