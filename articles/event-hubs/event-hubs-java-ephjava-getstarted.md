---
title: "Отправка и получение событий с помощью концентраторов событий Azure на языке Java | Документация Майкрософт"
description: "Следуйте указаниям этого руководства, чтобы приступить к использованию концентраторов событий Azure, отправке событий посредством Java и получению их с помощью EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 38e3be53-251c-488f-a856-9a500f41b6ca
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: c8950b09c412f1a709716e91d2a63edf047c5874


---
# <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Введение
Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в [обзоре концентраторов событий][Event Hubs overview].

В этом руководстве описано, как вводить сообщения в концентратор событий, используя консольное приложение на Java, и как параллельно извлекать их, используя библиотеку Event Processor Host для Java.

Чтобы выполнить задания из этого учебника, вам нужно следующее:

* Среда разработки Java. Для этого учебника предполагается использование среды [Eclipse](https://www.eclipse.org/).
* Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. Запустите проект **Receiver** .
   
   ![][21]
2. Запустите проект **Sender** .
   
   ![][22]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

* полный [пример приложения, использующего концентраторы событий][sample application that uses Event Hubs];
* пример [развертывания обработки событий с помощью концентраторов событий][Scale out Event Processing with Event Hubs];

Дополнительную информацию см. в [Центре разработчиков Java](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-java-ephjava-getstarted/java-send.png

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Jan17_HO4-->


