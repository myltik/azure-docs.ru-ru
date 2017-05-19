---
title: "Как выполнять запросы к данным таблиц в базе данных Azure Cosmos DB | Документация Майкрософт"
description: "Узнайте, как запрашивать данные таблиц в базе данных Azure Cosmos DB"
services: cosmosdb
documentationcenter: 
author: kanshiG
manager: jhubbard
editor: 
tags: 
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmosdb
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: govindk
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: cdd855aeac7dd30c52accb407289ca6db7dab4ae
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-how-to-query-with-the-table-api-preview"></a>Как выполнять запрос данных в базе данных Azure Cosmos DB с помощью API таблицы (предварительная версия)

[API таблицы](table-introduction.md) (предварительная версия) в базе данных Azure Cosmos DB поддерживает OData и запросы [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) к данным "ключ — значение" (таблицы).  

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * Запрос данных с помощью API таблицы.

## <a name="sample-table"></a>Пример таблицы

Запросы в этой статье используют следующий пример таблицы `People`:

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Walter | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

## <a name="about-the-table-api-preview"></a>Сведения об API таблицы (предварительная версия)

Так как база данных Azure Cosmos DB совместима с API хранилища таблиц Azure, сведения о запросе с помощью API таблицы см. в [этой статье] (https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities). 

Дополнительные сведения о расширенных возможностях, предлагаемых базой данных Azure Cosmos DB, см. в статьях [Introduction to Azure Cosmos DB: Table API](table-introduction.md) (Введение в базу данных Azure Cosmos DB: API таблицы) и [Azure Cosmos DB: Develop with the Table API in .NET](tutorial-develop-table-dotnet.md) (Разработка с помощью API таблицы на .NET в базе данных Azure Cosmos DB). 

## <a name="prerequisites"></a>Предварительные требования

Чтобы эти запросы работали, у вас должна быть учетная запись базы данных Azure Cosmos DB и данные сущности в контейнере. У вас их нет? Завершите [краткое руководство](https://aka.ms/acdbtnetqs) или [руководство разработчика](https://aka.ms/acdbtabletut), чтобы создать учетную запись и заполнить базу данных.

## <a name="querying-on-partition-key-and-row-key"></a>Запрос ключа секции и ключа строки
Так как свойства PartitionKey и RowKey образуют основной ключ сущности, вы можете использовать специальный синтаксис, чтобы идентифицировать сущность: 

**Запрос**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Результаты**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

В качестве альтернативы вы можете указать эти свойства как часть параметра `$filter`, как показано в следующем разделе. Обратите внимание, что в именах ключевых свойств и значениях констант учитывается регистр. Свойства PartitionKey и RowKey имеют тип String. 

## <a name="querying-with-an-odata-filter"></a>Выполнение запросов с помощью фильтра OData
При построении строки фильтра помните о следующих правилах: 

* Используйте логические операторы, определенные спецификацией протокола OData, чтобы сравнить свойство со значением. Обратите внимание, что нельзя сравнивать свойство и динамическое значение. Одна из частей выражения должна быть константой. 
* Имя свойства, оператор и значение константы должны быть разделены пробелами, закодированными в формате URL-адреса. Пробелы кодируются в формате URL-адреса как `%20`. 
* Во всех частях строки фильтра учитывается регистр. 
* Для получения допустимых результатов фильтра значения константы и свойства должны иметь одинаковый тип данных. Дополнительные сведения о поддерживаемых типах свойств см. в статье [Общие сведения о модели данных службы таблиц](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Вот пример запроса, который показывает, как фильтровать по PartitionKey и свойству Email с помощью `$filter` OData.

**Запрос**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Дополнительные сведения о том, как строить выражения фильтра для разных типов данных, см. в статье [Querying Tables and Entities](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities) (Запрос таблиц и сущностей).

**Результаты**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="querying-with-linq"></a>Выполнение запросов с помощью LINQ 
Вы также можете выполнить запрос с помощью LINQ, который преобразуется в соответствующие выражения запроса OData. Ниже приведен пример создания запросов с использованием пакета SDK .NET.

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * Научились запрашивать данные таблиц в базе данных Azure Cosmos с помощью API таблицы (предварительная версия). 

Теперь вы можете приступать к следующему руководству, чтобы узнать, как глобально распределять данные.

> [!div class="nextstepaction"]
> [Глобальное распределение данных](tutorial-global-distribution-documentdb.md)

