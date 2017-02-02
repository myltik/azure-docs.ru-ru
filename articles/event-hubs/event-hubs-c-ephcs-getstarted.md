---
title: "Приступая к работе с концентраторами событий в C и C# | Документация Майкрософт"
description: "Следуйте указаниям этого учебника, чтобы приступить к использованию концентраторов событий Azure, отправляющих события посредством C, и получению событий посредством C# с помощью EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5170c138-39ec-4eea-9925-e6902e5c425a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: fd7450985906dcf707a420d51b3a3f3b13786741
ms.openlocfilehash: 742bea46f7c4eba826523a49af4972dbb6db439b


---
# <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Введение
Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в разделе [Обзор концентраторов событий][Event Hubs overview].

В этом руководстве вы узнаете, как вводить сообщения в концентратор событий, используя консольное приложение C#, и как параллельно извлекать их, используя библиотеку C# [Event Processor Host][Event Processor Host].

Для работы с этим учебником необходимо следующее.

* Среда разработки C. В этом учебнике предполагается, что применяется стек gcc на [виртуальной машине Azure Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с Ubuntu 14.04. Инструкции для других сред будут предоставлены как внешние ссылки.
* Microsoft Visual Studio Express для Windows
* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В Visual Studio запустите проект **Receiver** и подождите, пока он запустит приемники для всех секций.
   
   ![][21]
2. Запустите программу **Sender** , после чего в окне приемника появятся события.
   
   ![][24]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

* полный [пример приложения, использующего концентраторы событий][sample application that uses Event Hubs];
* пример [развертывания обработки событий с помощью концентраторов событий][Scale out Event Processing with Event Hubs];
* [Обзор концентраторов событий Azure][Event Hubs overview].

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO1-->


