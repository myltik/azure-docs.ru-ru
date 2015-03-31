<properties 
	pageTitle="Приступая к работе с концентраторами событий" 
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события с помощью C#, и их получению в кластере Apache Storm" 
	services="service-bus" 
	documentationCenter="" 
	authors="fsautomata" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="core" 
	ms.tgt_pltfrm="csharp" 
	ms.devlang="csharp" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Приступая к работе с концентраторами событий

[AZURE.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

## Введение

Концентраторы событий - это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Для получения более подробной информации см [Обзор концентраторов событий][Общие сведения о концентраторах событий].

В этом уроке вы узнаете, как вводить сообщения в концентратор событий, используя консольное приложение на C#, и параллельно извлекать их при помощи Apache Storm.

Для работы с этим учебником необходимо следующее:

+ Microsoft Visual Studio Express 2013 для Windows.

+ Среда разработки Java, настроенная для запуска [Maven](http://maven.apache.org/). Для этого учебника предполагается использование среды [Eclipse](https://www.eclipse.org/)

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатное пробное использование Azure</a>.

## Создание концентратора событий

1. Войдите на [портал управления Azure][Портал управления Azure] и щелкните **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Служебная шина**, **Концентратор событий** и **Быстрое создание**.

   	![][1]

3. Введите имя для концентратора событий, выберите нужный регион и щелкните **Создать новый концентратор событий**.

   	![][2]

4. Щелкните ранее созданное пространство имен (обычно это **имя_концентратора_событий-ns**).

   	![][3]

5. Щелкните расположенную в верхней части страницы вкладку **Концентраторы событий** и выберите недавно созданный концентратор событий.

   	![][4]

6. Щелкните вкладку **Настройка** вверху, добавьте правило с именем **SendRule** с правами *Send*, добавьте еще одно правило с именем **ReceiveRule** с правами *Listen*, а затем щелкните **Сохранить**.

   	![][5]

7. На той же странице запишите ключи, сформированные для **ReceiveRule**.

   	![][6c]

8. Щелкните расположенную сверху вкладку **Панель мониторинга** и щелкните **Сведения о подключении**. Запишите значения двух строк подключения.

   	![][6]

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Запуск приложений

Теперь все готово к запуску приложений.

1.	Запустите класс **LogTopology** в Eclipse, а затем подождите, пока будут запущены приемники для всех разделов.

2.	Запустите проект **Sender** и нажмите в окне консоли клавишу **ВВОД**. После этого в окне приемника начнут отображаться события.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-getstarted/receive-storm1.png

<!-- Links -->
[Портал управления Azure]: https://manage.windowsazure.com/
[Узел обработчика событий]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Общие сведения о концентраторах событий]: http://msdn.microsoft.com/library/azure/dn836025.aspx

<!--HONumber=47-->
