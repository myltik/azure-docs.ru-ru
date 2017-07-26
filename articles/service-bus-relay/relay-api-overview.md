---
title: "Обзор API ретранслятора Azure | Документация Майкрософт"
description: "Обзор доступных интерфейсов API ретранслятора Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 8d93a0344adc3b0b7617f3a7d85124142d7a7555
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017

---

# <a name="available-relay-apis"></a>Доступные интерфейсы API ретранслятора

## <a name="runtime-apis"></a>API среды выполнения

В таблице ниже приведен список всех доступных клиентских библиотек среды выполнения ретранслятора.

Ознакомьтесь с разделом [Дополнительная информация](#additional-information), чтобы узнать больше о состоянии каждой библиотеки среды выполнения.

| Язык или платформа | Доступная функция | Пакет клиента | Репозиторий |
| --- | --- | --- | --- |
| .NET Standard | через гибридные подключения | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Ретранслятор WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Недоступно |
| Узел | через гибридные подключения | [`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Дополнительная информация

#### <a name="net"></a>.NET
В экосистеме .NET существует несколько сред выполнения, поэтому для концентраторов событий используется несколько библиотек .NET. Библиотеку .NET Standard можно запустить с помощью .NET Core или .NET Framework, а библиотеку .NET Framework можно запустить только в среде .NET Framework. Дополнительные сведения о версиях платформы .NET Framework см. в разделе [Версии платформ](/dotnet/articles/standard/frameworks#framework-versions).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о ретрансляторе Azure доступны по следующим ссылкам:
* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Вопросы и ответы по ретранслятору](relay-faq.md)
