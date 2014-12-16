<properties pageTitle="Приступая к работе с концентраторами событий" metaKeywords ="служебная шина Azure, концентратор событий, приступая к работе с концентраторами событий" description="Follow this tutorial to get started using Azure Event Hubs sending events with Java and receiving in C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Приступая к работе с концентраторами событий

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Концентраторы событий - масштабируемая система потребления, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени аналитики или в кластере хранилища.

Дополнительную информацию см. в разделе [Общие сведения о концентраторах событий].

В этом уроке вы узнаете, как вводить сообщения в концентратор событий, используя консольное приложение на Java, и параллельно извлекать их, используя библиотеку C# [Event Processor Host].

Для работы с этим учебником необходимо следующее.

+ Среда разработки Java. В этом учебнике предполагается, что используется [Eclipse](https://www.eclipse.org/).

+ Microsoft Visual Studio Express 2013 для Windows

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

6. Перейдите на вкладку **Настройка** в верхней части страницы, добавьте правило **SendRule** с правами *Send*, добавьте другое правило с именем **ReceiveRule** с правами *Manage, Send, Listen*, а затем нажмите кнопку **Сохранить**.

   	![][5]

7. На той же странице запишите созданные ключи для **SendRule**.

   	![][6b]

8. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Сведения о подключении**. Запишите две строки подключения.

   	![][6]

После создания концентратора событий и необходимо получить строки подключения для отправки и получения событий.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-java](../includes/service-bus-event-hubs-get-started-send-java.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Запуск приложений

Теперь все готово для запуска приложений.

1.	Запустите проект **Receiver** в Visual Studio, а затем подождите, пока будут запущены получатели для всех разделов.

   	![][21]

2.	Запустите проект **Sender**, нажмите клавишу **Ввод** в окне консоли, после чего в окне приемника событий появятся события.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6b]: ./media/service-bus-event-hubs-getstarted/create-event-hub6b.png


[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Портал управления Azure]: https://manage.windowsazure.com/
[Узел обработчика событий]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: http://msdn.microsoft.com/ru-ru/library/azure/dn836025.aspx
