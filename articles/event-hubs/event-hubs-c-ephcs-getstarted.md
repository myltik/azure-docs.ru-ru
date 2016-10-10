<properties
	pageTitle="Приступая к работе с концентраторами событий в C и C# | Microsoft Azure"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события посредством C, и получению событий посредством C# с помощью EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="jtaubensee"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="csharp"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="jotaub;sethm"/>

# Приступая к работе с концентраторами событий

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Введение

Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Подробнее: [Общие сведения о концентраторах событий][].

В этом учебнике вы узнаете, как вводить сообщения в концентратор событий, используя консольное приложение на C#, и как параллельно извлекать их, используя библиотеку C# [Event Processor Host][].

Для работы с этим учебником необходимо следующее.

+ Среда разработки C. В этом учебнике предполагается, что применяется стек gcc на [виртуальной машине Azure Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md) с Ubuntu 14.04. Инструкции для других сред будут предоставлены как внешние ссылки.

+ Microsoft Visual Studio Express для Windows

+ Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Запуск приложений

Теперь все готово к запуску приложений.

1.	В Visual Studio запустите проект **Receiver** и подождите, пока он запустит приемники для всех секций.

	![][21]

2.	Запустите программу **Sender**, после чего в окне приемника появятся события.

	![][24]

## Дальнейшие действия

Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

- Полный [пример приложения, использующего концентраторы событий][].
- Пример [развертывания обработки событий при помощи концентраторов событий][].
- [Решение для обмена сообщениями в очереди][] при помощи очередей служебной шины.
- [Обзор концентраторов событий][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: event-hubs-overview.md
[Общие сведения о концентраторах событий]: event-hubs-overview.md
[пример приложения, использующего концентраторы событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[развертывания обработки событий при помощи концентраторов событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Решение для обмена сообщениями в очереди]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

<!---HONumber=AcomDC_0928_2016-->