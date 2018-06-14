---
title: Обзор API концентраторов событий Azure | Документация Майкрософт
description: Обзор доступных интерфейсов API концентраторов событий Azure.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: abd44fd0c9cbfab2365b1552e3cd90e84a5348d7
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2017
ms.locfileid: "25957533"
---
# <a name="available-event-hubs-apis"></a>Доступные интерфейсы API концентраторов событий

В этой статье описывается набор доступных клиентов API, которые можно использовать для управления ресурсами концентраторов событий.

## <a name="runtime-apis"></a>API среды выполнения

Ниже приведено описание всех доступных в настоящее время клиентов в среде выполнения для концентраторов событий Azure. Хотя некоторые из этих библиотек включают в себя ограниченные функции управления, также существуют [специальные библиотеки](#management-apis), предназначенные для операций управления. Основной задачей этих библиотек является отправка сообщений в концентратор событий и получение их из него.

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
В экосистеме .NET существует несколько сред выполнения, поэтому для концентраторов событий используется несколько библиотек .NET. Библиотеку .NET Standard можно запустить с помощью .NET Core или .NET Framework, а библиотеку .NET Framework можно запустить только в среде .NET Framework. Дополнительные сведения о версиях платформы .NET Framework см. в разделе [Версии платформ](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

#### <a name="node"></a>Узел

В настоящее время доступна предварительная версия библиотеки Node.js. Она поддерживается как параллельный проект сотрудниками корпорации Майкрософт и внешними соавторами. Все добавляемые материалы, включая исходный код, принимаются и проверяются.

## <a name="management-apis"></a>API управления

Ниже приведен список всех доступных специальных библиотеки, предназначенных для операций управления. Ни одна из этих библиотек не содержит операций среды выполнения. Они предназначены только для управления сущностями концентраторов событий.

| Язык или платформа | Пакет управления | Репозиторий |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Event Hubs overview](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)