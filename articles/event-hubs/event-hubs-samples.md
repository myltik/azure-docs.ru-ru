---
title: Примеры концентраторов событий Azure | Документация Майкрософт
description: Примеры концентраторов событий Azure
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: sethm
ms.openlocfilehash: a581b7039a3631b7f1dc35816175242f892bd7dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
ms.locfileid: "29802793"
---
# <a name="event-hubs-samples"></a>Примеры концентраторов событий 

Набор примеров концентраторов событий Azure демонстрирует основные функции [концентраторов событий Azure](/azure/event-hubs/). В этой статье приведены категории примеров с описаниями и ссылками.

На момент написания этой статьи примеры концентраторов событий доступны по нескольким ссылкам:

- [Примеры кода для разработчиков на сайте MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

Дополнительные сведения о различных версиях платформы .NET Framework см. в статье [Требуемые версии .NET Framework](/dotnet/articles/standard/frameworks).

Со временем примеры будут добавляться, поэтому регулярно проверяйте наличие обновлений.

## <a name="net-standard"></a>.NET Standard

В следующих примерах демонстрируются способы отправки и получения событий с помощью [клиента концентраторов событий](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) для [библиотеки .NET Standard](/dotnet/articles/standard/library).

### <a name="send-events"></a>Отправка событий 

В [этом примере](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) показано, как написать консольное приложение .NET Core, которое отправляет события в концентратор событий.

### <a name="receive-events"></a>Получение событий 

В [этом примере](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) содержится консольное приложение .NET Core, которое получает сообщения из концентратора событий, используя узел обработчика событий.

## <a name="net-framework"></a>.NET Framework   

В этих примерах демонстрируются другие функции концентраторов событий Azure, предназначенные для [библиотеки .NET Framework](/dotnet/framework/index).
 
### <a name="notify-users-of-events-received"></a>Уведомление пользователей о полученных событиях

Пример приложения [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) уведомляет пользователей о данных, полученных с датчиков или из других систем.

### <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий 

В [этом примере](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) демонстрируются базовые возможности концентраторов событий: их создание, отправка событий в концентратор событий и получение событий с помощью [узла обработчика событий](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

### <a name="scale-out-event-processing"></a>Развертывание обработки событий 

В [этом примере](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) демонстрируется, как с помощью [узла обработчика событий](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) распределить рабочую нагрузку потребления потока концентраторов событий. В нем показано, как реализовать объекты **EventProcessor** и **EventProcessorFactory** для управления потоком событий. 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о версиях платформы .NET Framework см. по следующим ссылкам:

- [Платформы и целевые объекты](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 и 4.5](/dotnet/framework/index)

Чтобы узнать больше о концентраторах событий, обратитесь к следующим статьям:

- [Event Hubs overview](event-hubs-what-is-event-hubs.md)
- [Особенности концентраторов событий](event-hubs-features.md)
- [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)