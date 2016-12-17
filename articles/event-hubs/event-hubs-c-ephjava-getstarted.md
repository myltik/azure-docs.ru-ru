---
title: "Приступая к работе с концентраторами событий в C | Документация Майкрософт"
description: "Следуйте указаниям этого руководства, чтобы приступить к использованию концентраторов событий Azure, отправке событий посредством C и получению событий посредством Java с помощью узла обработчика событий."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: aa6553f9-e12e-4568-9bf3-667f1c47a6cf
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: e8e3708989f85a7a0d895140baad5d6370ba3146


---
# <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Введение
Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в статье [Обзор концентраторов событий][Обзор концентраторов событий].

В этом руководстве вы узнаете, как принимать сообщения в концентратор событий, используя консольное приложение на C#, и как параллельно извлекать их, используя библиотеку C# [узла обработчика событий][Event Processor Host].

Для работы с этим учебником необходимо следующее.

* Среда разработки C. В этом учебнике предполагается, что применяется стек gcc на [виртуальной машине Azure Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с Ubuntu 14.04. Инструкции для других сред будут предоставлены как внешние ссылки.
* Microsoft Visual Studio Express для Windows
* Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. Запустите проект **Receiver** .
   
   ![][21]
2. Запустите программу **Sender** , после чего в окне приемника появятся события.
   
   ![][24]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы создали рабочее приложение, которое создает концентратор событий и отправляет и получает данные, можно перейти к следующим сценариям:

* Полный [пример приложения, использующего концентраторы событий][пример приложения, использующего концентраторы событий].
* Пример [масштабирования обработки событий с помощью концентраторов событий][развертывания обработки событий при помощи концентраторов событий].
* [Обзор концентраторов событий][Обзор концентраторов событий]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[классический портал Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Обзор концентраторов событий]: event-hubs-overview.md
[пример приложения, использующего концентраторы событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[развертывания обработки событий при помощи концентраторов событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


