<properties pageTitle="Приступая к работе с концентраторами событий" metaKeywords="Azure Service Bus, Event Hub, getting started Event Hubs" description="Follow this tutorial to get started using Azure Event Hubs with C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Приступая к работе с концентраторами событий

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Концентратор событий - это высокомасштабируемая приемная система, которая может обрабатывать миллионы событий в секунду, позволяя приложению обрабатывать и анализировать огромное количество данных, создаваемых подключенными устройствами и приложениями. Данные, собранные в концентраторах событий, можно преобразовывать и сохранять с использованием любого средства аналитики, работающего в режиме реального времени, и кластера хранения. Дополнительную информацию о концентраторах событий см. в документе [Руководство по программированию концентраторов событий]. 

Другую дополнительную информацию см. в статье [Event Hubs Overview] (Обзор концентраторов событий).

В этом учебнике рассказывается, как принимать сообщения в концентраторе событий с помощью консольного приложения на языке C# и параллельно извлекать их с использованием библиотеки [Event Processor Host] на языке C#.

Чтобы пройти этот учебник требуется:

+ Microsoft Visual Studio Express 2013 для Windows;

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительную информацию см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатное пробное использование Azure</a>.

## Создание концентратора событий

1. Войдите на [портал управления Azure] и щелкните **СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор событий** и **Быстрое создание**.

   	![][1]

3. Введите имя для концентратора событий, выберите нужный регион и щелкните элемент **Создать новый концентратор событий**.

   	![][2]

4. Выберите ранее созданное пространство имен (обычно это **имя_концентратора_событий-ns**).

   	![][3]

5. Откройте расположенную в верхней части страницы вкладку **Концентраторы событий** и выберите недавно созданный концентратор событий.

   	![][4]

6. Выберите расположенную сверху вкладку **Настройка**, добавьте правило **SendRule**, задав для него права "Отправка", и правило **ReceiveRule**, задав для него права "Управление", "Отправка" и "Прослушивание", а затем щелкните **Сохранить**.

   	![][5]

7. Откройте расположенную в верхней части страницы вкладку **Панель мониторинга** и щелкните **Сведения о подключении**. Запишите значения двух строк подключения.

   	![][6]

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Запуск приложений

Теперь все готово к запуску приложений.

1.	В Visual Studio запустите проект **Receiver** и подождите, пока он запустит приемники для всех разделов.

   	![][21]

2.	Запустите проект **Sender** и нажмите в окнах консоли клавишу **ВВОД**. После этого в окне приемника начнут отображаться события.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Портал управления Azure]: https://manage.windowsazure.com/
[Event Processor Host] (Библиотека Event Processor Host): https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs Overview] (Обзор концентраторов событий): http://msdn.microsoft.com/ru-ru/library/azure/dn836025.aspx

<!--HONumber=35_1-->
