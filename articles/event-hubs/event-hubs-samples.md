---
title: "Примеры концентраторов событий Azure | Документация Майкрософт"
description: "Примеры концентраторов событий"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 5b02054546ebf25766be6bb2837aebc502b67a67
ms.openlocfilehash: 9c50b7caefe06bceac8035a0d274e13a96547fea

---

# <a name="event-hubs-samples"></a>Примеры концентраторов событий 

Примеры концентраторов событий демонстрируют основные функции [концентраторов событий Azure](/azure/event-hubs/). В этой статье приведены категории примеров с описаниями и ссылками.

На момент написания этой статьи примеры концентраторов событий доступны по нескольким ссылкам:

- [Примеры кода для разработчиков на сайте MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples)

Дополнительные сведения о различных версиях платформы .NET Framework см. в статье [Платформы и целевые объекты](/dotnet/articles/standard/frameworks).

Со временем примеры будут добавляться, поэтому регулярно проверяйте наличие обновлений.

## <a name="net-standard"></a>.NET Standard

В следующих примерах демонстрируются способы отправки и получения событий с помощью [клиента концентраторов событий](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) для [библиотеки .NET Standard](/dotnet/articles/standard/library).

### <a name="send-events"></a>Отправка событий 

В [этом примере](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender) показано, как написать консольное приложение для .NET Core, которое отправляет события в концентратор событий.

### <a name="receive-events"></a>Получение событий 

В [этом примере](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleEphReceiver) содержится консольное приложение .NET Core, которое получает сообщения из концентратора событий, используя [узел обработчика событий](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/). 

## <a name="net-framework"></a>.NET Framework   

В этих примерах демонстрируются другие функции концентраторов событий Azure, предназначенные для [библиотеки .NET Framework](https://msdn.microsoft.com/library/w0x726c2.aspx).
 
### <a name="notify-users-of-events-received"></a>Уведомление пользователей о полученных событиях

Пример приложения [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) уведомляет пользователей о данных, полученных с датчиков или из других систем.

### <a name="get-started-with-event-hubs"></a>Приступая к работе с концентраторами событий 

В [этом примере](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) демонстрируются базовые возможности концентраторов событий: их создание, отправка событий в концентратор событий и получение событий с помощью [узла обработчика событий](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

### <a name="scale-out-event-processing"></a>Развертывание обработки событий 

В [этом примере](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) демонстрируется, как с помощью [узла обработчика событий](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) распределить рабочую нагрузку потребления потока концентраторов событий. В нем показано, как реализовать объекты **EventProcessor** и **EventProcessorFactory** для управления потоком событий. 

###  <a name="pull-data-from-sql-into-an-event-hub"></a>Извлечение данных из SQL в концентратор событий

В [этом примере](https://github.com/Azure-Samples/event-hubs-dotnet-import-from-sql) показано, как извлечь данные из таблицы SQL и передать их в концентратор событий для использования в качестве входных данных для нижестоящих аналитических приложений.

### <a name="pull-web-data-into-an-event-hub"></a>Извлечение веб-данных в концентратор событий 

В [этом примере](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb) показано, как извлечь данные из общедоступных каналов (например из информационного канала Министерства транспорта США) и передать их в концентратор событий.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о версиях платформы .NET Framework см. по следующим ссылкам:

- [Платформы и целевые объекты](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 и 4.5](https://msdn.microsoft.com/library/w0x726c2.aspx)

Чтобы узнать больше о концентраторах событий, обратитесь к следующим статьям:

- [Обзор концентраторов событий](event-hubs-what-is-event-hubs.md)
- [Создание концентратора событий](event-hubs-create.md)
- [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


