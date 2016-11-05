---
title: Приступая к работе с концентраторами событий в Java | Microsoft Docs
description: Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события посредством Java, и получению событий посредством C# с помощью EventProcessorHost.
services: event-hubs
documentationcenter: ''
author: jtaubensee
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: jotaub;sethm

---
# <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Введение
Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в статье [Обзор концентраторов событий][Обзор концентраторов событий].

В этом руководстве описано, как вводить сообщения в концентратор событий, используя консольное приложение на Java, и как параллельно извлекать их, используя библиотеку C# [Event Processor Host][Event Processor Host] .

Чтобы выполнить задания из этого учебника, вам нужно следующее:

* Среда разработки Java. Для этого учебника предполагается использование среды [Eclipse](https://www.eclipse.org/).
* [Microsoft Visual Studio](http://visualstudio.com)
* Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. Запустите проект **Receiver** в Visual Studio, а затем подождите, пока будут запущены приемники для всех секций.
   
   ![][21]
2. Запустите проект **Sender** .
   
   ![][22]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

* Полный [пример приложения, использующего концентраторы событий][пример приложения, использующего концентраторы событий].
* Пример [развертывания обработки событий при помощи концентраторов событий][развертывания обработки событий при помощи концентраторов событий] .

Дополнительную информацию см. в [Центре разработчиков Java](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/java-send.png

<!-- Links -->
[классический портал Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: event-hubs-overview.md
[пример приложения, использующего концентраторы событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[развертывания обработки событий при помощи концентраторов событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Oct16_HO2-->


