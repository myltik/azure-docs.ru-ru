---
title: "Приступая к работе с концентраторами событий в C | Документация Майкрософт"
description: "Следуйте указаниям этого руководства, чтобы приступить к работе с концентраторами событий Azure в C# с помощью узла обработчика событий."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 2ec2378a-34f7-44c3-b976-cc444c98c338
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/07/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 015dbab81f807cd05d1fedb97f5fc62a012f2084
ms.openlocfilehash: 54aaf68864fc83501b860ecc3daef1c0216c0140


---
# <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Введение
Концентраторы событий — это служба, которая обрабатывает большие объемы данных телеметрии о событиях, поступающих от подключенных устройств и приложений. После сбора дынных в концентраторах событий их можно сохранить с помощью кластера хранилища или преобразовать с помощью поставщика аналитики в реальном времени. Эта возможность сбора и обработки большого объема данных о событиях является ключевым компонентом в современных архитектурах приложений, включая "Интернет вещей".

В этом учебнике показано, как использовать портал Azure для создания концентратора событий. В нем также показано, как собирать сообщения в концентратор событий, используя консольное приложение на C#, и как параллельно извлекать их, используя библиотеку C# [Event Processor Host][Event Processor Host].

Для работы с этим учебником требуется:

* [Microsoft Visual Studio](http://visualstudio.com)
* Активная учетная запись Azure. Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/free/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В Visual Studio откройте созданный ранее проект **Receiver** .
2. Щелкните правой кнопкой мыши решение **Receiver**, выберите пункт **Добавить**, а затем — **Существующий проект**.
3. Найдите существующий файл Sender.csproj и дважды щелкните его, чтобы добавить в решение.
4. Еще раз щелкните правой кнопкой мыши решение **Receiver** и выберите пункт **Свойства**. Откроется страница свойств проекта **Receiver** .
5. Щелкните **Запускаемый проект** и нажмите кнопку **Несколько запускаемых проектов**. В поле **Действие** для проектов **Receiver** и **Sender** задайте значение **Запустить**.
   
    ![][19]
6. Щелкните **Зависимости проектов**. В поле **Проекты** выберите значение **Sender**. Убедитесь, что в поле **Зависит от** выбрано значение **Receiver**.
   
    ![][20]
7. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства**.
8. Нажмите клавишу F5, чтобы запустить проект **Receiver** в Visual Studio, и подождите, пока он запустит приемники для всех секций.
   
   ![][21]
9. Проект **Sender** запустится автоматически. В окне консоли нажмите клавишу **ВВОД**. После этого в окне проекта Receiver начнут появляться события.
   
   ![][22]

В окне **Sender** нажмите клавиши **CTRL+C**, чтобы закрыть приложение Sender, а затем в окне Receiver нажмите клавишу **ВВОД**, чтобы завершить работу этого приложения.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

* полный [пример приложения, использующего концентраторы событий][sample application that uses Event Hubs];
* [Event Processor Host](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* пример [развертывания обработки событий с помощью концентраторов событий][Scale out Event Processing with Event Hubs];
* [Обзор концентраторов событий Azure][Event Hubs overview].

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Dec16_HO2-->


