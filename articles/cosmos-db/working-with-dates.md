---
title: Работа с датами в Azure Cosmos DB | Документация Майкрософт
description: Сведения о работе с датами в Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: sngun
ms.openlocfilehash: 3998b2d1575cfe80c01a44df1b9999de2ce1d548
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34616106"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Работа с датами в Azure Cosmos DB
Azure Cosmos DB обеспечивает гибкость схемы данных и обширные возможности индексирования благодаря использованию собственной модели данных на основе [JSON](http://www.json.org). Все ресурсы Azure Cosmos DB, включая базы данных, коллекции, документы и хранимые процедуры, моделируются и хранятся в виде документов JSON. Чтобы гарантировать свойство переносимости, JSON (и Azure Cosmos DB) поддерживает только небольшой набор основных типов: строка, число, логическое значение, массив, объект и Null. Но JSON благодаря своей гибкости позволяет разработчикам и платформам моделировать и более сложные типы, составляя их из поддерживаемых примитивов в виде объектов или массивов. 

Помимо основных типов многим приложениям требуется формат [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) для представления дат и отметок времени. В этой статье описывается, как разработчики могут хранить, извлекать и искать даты в Azure Cosmos DB с помощью пакета SDK для .NET.

## <a name="storing-datetimes"></a>Хранение значений даты и времени
По умолчанию [пакет SDK для Azure Cosmos DB](sql-api-sdk-dotnet.md) сериализует значения даты и времени в формате строк [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874). Многим приложениям вполне достаточно используемого по умолчанию строкового представления дат и времени, поскольку оно:

* позволяет сравнивать строки, а следовательно и сохранять относительный порядок значений даты и времени, после преобразования в строки; 
* не требует создавать пользовательский код или специальные атрибуты для преобразования JSON;
* сохраняет даты в понятном для человека формате JSON;
* Позволяет воспользоваться преимуществами индексов Azure Cosmos DB для быстрой обработки запросов.

Например, следующий фрагмент кода сохраняет объект `Order`, содержащий два свойства в формате даты и времени (`ShipDate` и `OrderDate`) в виде документа, использующего пакет SDK для .NET.

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Этот документ хранится в Azure Cosmos DB в следующем виде:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Кроме того, вы можете хранить даты как отметки времени Unix, то есть как число, представляющее количество секунд, прошедших с 1 января 1970 года. Внутреннее свойство Azure Cosmos DB для отметки времени (`_ts`) использует именно этот подход. Для сериализации значений даты и времени в числа используйте класс [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx). 

## <a name="indexing-datetimes-for-range-queries"></a>Индексирование значений даты и времени для запросов в диапазоне
Запросы в диапазоне часто применяются для значений даты и времени. Например, они позволяют найти все заказы, созданные не позднее вчерашнего дня, или все заказы, отправленные за последние пять минут. Для эффективного выполнения таких запросов вам потребуется коллекция для индексирования строк по диапазонам.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Дополнительные сведения о настройке политик индексирования см. в статье [Как работает индексирование данных в Azure Cosmos DB?](indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>Запрос по датам и времени в LINQ
Пакет SDK .NET для SQL автоматически поддерживает запросы с использованием LINQ к данным, хранящимся в Azure Cosmos DB. Например следующий фрагмент кода демонстрирует запрос LINQ, который отбирает все заказы, доставленные за последние три дня.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Дополнительные сведения о языке запросов SQL в Azure Cosmos DB и о поставщике LINQ см. в статье [SQL-запросы для API DocumentDB в Azure Cosmos DB](sql-api-sql-query.md).

В этой статье мы рассмотрели способы хранения, индексирования и получения значений дат и времени в Azure Cosmos DB.

## <a name="next-steps"></a>Дальнейшие действия
* Скачайте и запустите [примеры кода, хранящиеся на GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples).
* Дополнительные сведения об [SQL-запросах](sql-api-sql-query.md)
* Дополнительные сведения о политиках индексации в Azure Cosmos DB см. в [этой статье](indexing-policies.md).
