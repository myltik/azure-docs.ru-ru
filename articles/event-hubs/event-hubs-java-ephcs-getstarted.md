<properties
	pageTitle="Приступая к работе с концентраторами событий в Java | Microsoft Azure"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события посредством Java, и получению событий посредством C# с помощью EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="sethm"/>

# Приступая к работе с концентраторами событий

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Введение

Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в статье [Обзор концентраторов событий][].

В этом руководстве описано, как вводить сообщения в концентратор событий, используя консольное приложение на Java, и как параллельно извлекать их, используя библиотеку C# [Event Processor Host][].

Чтобы выполнить задания из этого учебника, вам нужно следующее:

+ Среда разработки Java. Для этого учебника предполагается использование среды [Eclipse](https://www.eclipse.org/).

+ Microsoft Visual Studio Express для Windows

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Подробнее: <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

## Создание концентратора событий

1. Войдите на [классический портал Azure][] и в нижней части экрана нажмите кнопку **Создать**.

2. Последовательно щелкните **Службы приложений**, **Служебная шина**, **Концентратор событий** и **Быстрое создание**.

	![][1]

3. Введите имя для концентратора событий, выберите нужный регион и щелкните **Создать новый концентратор событий**.

	![][2]

4. Щелкните ранее созданное пространство имен (обычно это ***имя концентратора событий*-ns**).

	![][3]

5. Щелкните расположенную в верхней части страницы вкладку **Концентраторы событий** и выберите недавно созданный концентратор событий.

	![][4]

6. Выберите расположенную вверху страницы вкладку **Настройка**, добавьте правило с именем **SendRule**, задав для него права *Отправка*, и правило с именем **ReceiveRule**, задав для него права *Управление, отправка, прослушивание*, а затем нажмите кнопку **Сохранить**.

	![][5]

7. На той же странице запишите созданные ключи для **SendRule**.

	![][7]

8. Щелкните расположенную в верхней части страницы вкладку **Панель мониторинга** и щелкните **Сведения о подключении**. Запишите две строки подключения.

	![][6]

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Запуск приложений

Теперь все готово к запуску приложений.

1.	Запустите проект **Receiver** в Visual Studio, а затем подождите, пока будут запущены приемники для всех секций.

	![][21]

2.	Запустите проект **Sender** и нажмите в окне консоли клавишу **ВВОД**. После этого в окне приемника начнут отображаться события.

	![][22]

## Дальнейшие действия

Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

- Полный [пример приложения, использующего концентраторы событий][].
- Пример [развертывания обработки событий при помощи концентраторов событий][].
- [Решение для обмена сообщениями в очереди][] при помощи очередей служебной шины.

Дополнительные сведения см. в [Центре разработчика Java](/develop/java/).

<!-- Images. -->
[1]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6.png
[7]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6b.png


[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[классический портал Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: event-hubs-overview.md
[пример приложения, использующего концентраторы событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[развертывания обработки событий при помощи концентраторов событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Решение для обмена сообщениями в очереди]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0323_2016-->