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
ms.date: 02/28/2018
ms.author: sngun
ms.openlocfilehash: a8e4ac763b9a16210d62fc080aaf8917c1eefc33
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
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
> 
> 

Пакет SDK Async Java для API-интерфейса SQL отличается от пакета SDK Java для API-интерфейса SQL возможностью выполнять асинхронные операции с поддержкой [библиотеки Netty](http://netty.io/). Существующий [пакет SDK Java для API-интерфейса SQL](sql-api-sdk-java.md) не поддерживает асинхронные операции. 

<table>

<tr><td>**Скачивание пакета SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API Java](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Участие в разработке пакета SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Приступите к работе**</td><td>[Начало работы с пакетом SDK для Async Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Пример кода**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Советы по улучшению производительности**</td><td>[Файл сведений Github](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Минимальная поддерживаемая среда выполнения**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Пакет SDK в общедоступной версии с комплексной поддержкой неблокирующих операций ввода-вывода и [библиотеки Netty](http://netty.io/) в режиме шлюза. 

## <a name="release-and-retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии.

Любые запросы к Cosmos DB с помощью выведенного из эксплуатации пакета SDK будут отклонены службой.

<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.0.0](#1.0.0) |27 февраля 2018 г|--- |

## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

