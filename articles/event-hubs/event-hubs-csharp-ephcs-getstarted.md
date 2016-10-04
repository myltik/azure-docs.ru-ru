<properties
	pageTitle="Приступая к работе с концентраторами событий в C# | Microsoft Azure"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure в C# с помощью EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="jtaubensee"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/02/2016"
	ms.author="jotaub;sethm"/>

# Приступая к работе с концентраторами событий

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Введение

Концентраторы событий — это служба, которая обрабатывает большие объемы данных телеметрии о событиях, поступающих от подключенных устройств и приложений. После сбора дынных в концентраторах событий их можно сохранить с помощью кластера хранилища или преобразовать с помощью поставщика аналитики в реальном времени. Эта возможность сбора и обработки большого объема данных о событиях является ключевым компонентом в современных архитектурах приложений, включая "Интернет вещей".

В этом руководстве показано, как использовать классический портал Azure для создания концентратора событий. В нем также показано, как собирать сообщения в концентратор событий, используя консольное приложение на C#, и как параллельно извлекать их, используя библиотеку C# [Event Processor Host][].

Для работы с этим учебником требуется:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Активная учетная запись Azure. Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Запуск приложений

Теперь все готово к запуску приложений.

1. В Visual Studio откройте созданный ранее проект **Receiver**.

2. Щелкните правой кнопкой мыши решение **Receiver**, выберите пункт **Добавить**, а затем — **Существующий проект**.
 
3. Найдите существующий файл Sender.csproj и дважды щелкните его, чтобы добавить в решение.
 
4. Еще раз щелкните правой кнопкой мыши решение **Receiver** и выберите пункт **Свойства**. Откроется страница свойств проекта **Receiver**.

5. Щелкните **Запускаемый проект** и нажмите кнопку **Несколько запускаемых проектов**. В поле **Действие** для обоих проектов — **Receiver** и **Sender** — задайте значение **Запустить**.

	![][19]

6. Щелкните **Зависимости проектов**. В поле **Проекты** выберите значение **Sender**. Убедитесь, что в поле **Зависит от** выбрано значение **Receiver**.

	![][20]

7. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства**.

1.	Нажмите клавишу F5, чтобы запустить проект **Receiver** в Visual Studio, и подождите, пока он запустит приемники для всех секций.

	![][21]

2.	Проект **Sender** запустится автоматически. В окне консоли нажмите клавишу **ВВОД**. После этого в окне проекта Receiver начнут появляться события.

	![][22]

В окне **Sender** нажмите клавиши **CTRL+C**, чтобы закрыть приложение Sender, а затем в окне Receiver нажмите клавишу **ВВОД**, чтобы завершить работу этого приложения.

## Дальнейшие действия

Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

- Полный [пример приложения, использующего концентраторы событий][].
- Пример [развертывания обработки событий при помощи концентраторов событий][].
- [Решение для обмена сообщениями в очереди][] при помощи очередей служебной шины.
- [Обзор концентраторов событий][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: event-hubs-overview.md
[пример приложения, использующего концентраторы событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[развертывания обработки событий при помощи концентраторов событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Решение для обмена сообщениями в очереди]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0928_2016-->