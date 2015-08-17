<properties
	pageTitle="Приступая к работе с концентраторами событий"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure в C# с помощью EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="csharp"
	ms.devlang="csharp"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="sethm"/>

# Приступая к работе с концентраторами событий

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Введение

Концентратор событий — это служба, которую можно использовать для обработки больших объемов данных о событиях в подключенных устройствах и приложениях. После сбора дынных в концентраторах событий их можно сохранить с помощью кластера хранилища или преобразовать с помощью поставщика аналитики в реальном времени. Эта возможность сбора и обработки большого объема данных о событиях является ключевым компонентом в современных архитектурах приложений, включая Интернет вещей.

В этом учебнике показано, как использовать портал управления Azure для создания концентратора событий. В нем также показано, как собирать сообщения в концентратор событий, используя консольное приложение на C#, и как параллельно извлекать их, используя библиотеку C# [Event Processor Host].

Чтобы пройти этот учебник, требуется:

+ Microsoft Visual Studio 2013 или Microsoft Visual Studio Express 2013 для Windows.

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Подробнее: <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

## Создание концентратора событий

1. Войдите на [портал управления Azure] и щелкните элемент **СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните **Службы приложений**, **Служебная шина**, **Концентратор событий** и **Быстрое создание**.

   	![][1]

3. Введите имя для концентратора событий, выберите нужный регион и щелкните **Создать новый концентратор событий**.

   	![][2]

4. Щелкните ранее созданное пространство имен (обычно это ***имя концентратора событий*-ns**).

   	![][3]

5. Щелкните расположенную в верхней части страницы вкладку **Концентраторы событий** и выберите недавно созданный концентратор событий.

   	![][4]

6. Выберите расположенную вверху вкладку **Настройка**, добавьте правило **SendRule**, задав для него права *Отправка*, и правило **ReceiveRule**, задав для него права *Управление, отправка, прослушивание*, а затем нажмите кнопку **Сохранить**.

   	![][5]

7. Щелкните расположенную в верхней части страницы вкладку **Панель мониторинга** и щелкните **Сведения о подключении**. Запишите две строки подключения или скопируйте их для использования далее в этом учебнике.

   	![][6]

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Запуск приложений

Теперь все готово к запуску приложений.

1.	В Visual Studio запустите проект **Receiver** и подождите, пока он запустит приемники для всех разделов.

   	![][21]

2.	Запустите проект **Sender** и нажмите в окнах консоли клавишу **ВВОД**. После этого в окне приемника начнут отображаться события.

   	![][22]

## Дальнейшие действия

Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

- Полный [пример приложения, использующего концентраторы событий].
- Пример [развертывания обработки событий при помощи концентраторов событий].
- [Решение для обмена сообщениями в очереди] при помощи очередей служебной шины.

<!-- Images. -->
[1]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub6.png

[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[портал управления Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs Overview]: http://msdn.microsoft.com/library/azure/dn836025.aspx
[пример приложения, использующего концентраторы событий]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[развертывания обработки событий при помощи концентраторов событий]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Решение для обмена сообщениями в очереди]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=August15_HO6-->