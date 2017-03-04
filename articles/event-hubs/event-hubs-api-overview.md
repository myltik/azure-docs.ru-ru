---
title: "Обзор API концентраторов событий Azure | Документация Майкрософт"
description: "Обзор доступных интерфейсов API концентраторов событий Azure."
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: 5a360462288e5df6e0ede5f11adabba158a9dd57
ms.lasthandoff: 02/02/2017

---

# <a name="available-event-hubs-apis"></a>Доступные интерфейсы API концентраторов событий

## <a name="runtime-apis"></a>API среды выполнения

Ниже приведен список всех доступных клиентских библиотек среды выполнения концентраторов событий. Хотя некоторые из этих библиотек включают в себя ограниченные функции управления, также существуют [специальные библиотеки](#management-apis), предназначенные для операций управления. Основной задачей этих библиотек является отправка сообщений в концентратор событий и получение их из него.

Ознакомьтесь с разделом [Дополнительная информация](#additional-information), чтобы узнать больше о текущем состоянии каждой библиотеки среды выполнения.

| Язык или платформа | Пакет клиента | Пакет EventProcessorHost | Репозиторий |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Недоступно |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Узел | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Недоступно | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | Недоступно | Недоступно | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Дополнительная информация

#### <a name="net"></a>.NET
В экосистеме .NET существует несколько сред выполнения, поэтому для концентраторов событий используется несколько библиотек .NET. Библиотеку .NET Standard можно запустить с помощью .NET Core или .NET Framework, а библиотеку .NET Framework можно запустить только в среде .NET Framework. Дополнительные сведения о версиях .NET Framework см. в разделе [Версии платформ](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

#### <a name="node"></a>Узел

В настоящее время доступна предварительная версия библиотеки Node.js. Она поддерживается как параллельный проект сотрудниками корпорации Майкрософт и внешними соавторами. Все добавляемые материалы, включая исходный код, принимаются и проверяются.

## <a name="management-apis"></a>API управления

Ниже приведен список всех доступных специальных библиотеки, предназначенных для операций управления. Ни одна из этих библиотек не содержит операций среды выполнения. Они предназначены только для управления сущностями концентраторов событий.

| Язык или платформа | Пакет управления | Репозиторий |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Обзор концентраторов событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)
