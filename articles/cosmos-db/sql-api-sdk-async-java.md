---
title: Azure Cosmos DB. Интерфейс API, пакет SDK и ресурсы для SQL Async Java | Документация Майкрософт
description: Сведения о пакете SDK и API-интерфейсе SQL Async Java, включая даты выхода и прекращения использования, а также изменения, внесенные в каждую версию пакета SDK SQL Async Java для Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/20/2018
ms.author: sngun
ms.openlocfilehash: 25a84c42430c76d296e12d3f83040fa18febdcb1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB: заметки о выпуске и материалы по пакету SDK Async Java для API-интерфейса SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor — Java](sql-api-sdk-bulk-executor-java.md)

Пакет SDK Async Java для API-интерфейса SQL отличается от пакета SDK Java для API-интерфейса SQL возможностью выполнять асинхронные операции с поддержкой [библиотеки Netty](http://netty.io/). Существующий [пакет SDK Java для API-интерфейса SQL](sql-api-sdk-java.md) не поддерживает асинхронные операции. 

<table>

<tr><td>**Скачивание пакета SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API Java](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Участие в разработке пакета SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Начало работы**</td><td>[Начало работы с пакетом SDK для Async Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Пример кода**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Советы по улучшению производительности**</td><td>[Файл сведений Github](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Минимальная поддерживаемая среда выполнения**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* В запросах добавлена поддержка обратной реакции.
* В запросах добавлена поддержка идентификатора диапазона для ключей секции.
* Внесено исправление, позволяющее использовать больший маркер продолжения в заголовке запроса (исправление ошибки 24 в GitHub).
* Зависимость Netty обновлена до версии 4.1.22.Final, чтобы обеспечить завершение работы виртуальной машины Java после выполнения основного потока.
* Внесено исправление, позволяющее избежать передачи маркера сеанса при считывании ресурсов главной ветви.
* Добавлены другие примеры.
* Добавлены другие сценарии тестирования производительности.
* Исправлены файлы заголовков Java для правильного создания документа Java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Пакет SDK в общедоступной версии с комплексной поддержкой неблокирующих операций ввода-вывода и [библиотеки Netty](http://netty.io/) в режиме шлюза. 

## <a name="release-and-retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые компоненты, функции и средства оптимизации добавлены только в текущий пакет SDK. Поэтому рекомендуем как можно раньше обновлять систему до последней версии пакета SDK.

Любые запросы к Cosmos DB с помощью выведенного из эксплуатации пакета SDK будут отклонены службой.

<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.0.1](#1.0.1) |20 апреля 2018 г.|--- |
| [1.0.0](#1.0.0) |27 февраля 2018 г|--- |

## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

