---
title: Подготовка пропускной способности для Azure Cosmos DB | Документация Майкрософт
description: Узнайте, как задать подготовленную пропускную способность для контейнеров, коллекций, графов и таблиц Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 0a53bb0a23fae386abbe71de944b073cbb93d502
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Настройка и получение пропускной способности контейнеров Azure Cosmos DB

Пропускную способность для контейнеров Azure Cosmos DB можно настроить на портале Azure или с помощью клиентских пакетов SDK. 

В следующей таблице указана пропускная способность, доступная для каждой коллекции.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Односекционный контейнер</strong></p></td>
            <td valign="top"><p><strong>Секционированный контейнер</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Минимальная пропускная способность</p></td>
            <td valign="top"><p>400 единиц запроса в секунду</p></td>
            <td valign="top"><p>1000 единиц запроса в секунду</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Максимальная пропускная способность</p></td>
            <td valign="top"><p>10 000 единиц запроса в секунду</p></td>
            <td valign="top"><p>Без ограничений</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Настройка пропускной способности с помощью портала Azure

1. В новом окне откройте [портал Azure](https://portal.azure.com).
2. На панели слева щелкните **Azure Cosmos DB** или выберите внизу пункт **Все службы**, перейдите к разделу **Базы данных** и выберите **Azure Cosmos DB**.
3. Выберите учетную запись Cosmos DB.
4. В новом окне в меню навигации щелкните **Обозреватель данных**.
5. В новом окне разверните узел базы данных и контейнера и щелкните **Scale & Settings** (Параметры масштабирования).
6. В новом окне в поле **Пропускная способность** введите новое значение пропускной способности, а затем щелкните **Сохранить**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Настройка пропускной способности с помощью API SQL для .NET

Следующий фрагменте кода получает текущую пропускную способность и изменяет ее на 500 единиц запросов в секунду. Полный пример кода см. в проекте [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) на сайте GitHub.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Настройка пропускной способности с помощью API SQL для Java

Следующий фрагменте кода получает текущую пропускную способность и изменяет ее на 500 единиц запросов в секунду. Полный пример кода см. в файле [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) на сайте GitHub. 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Получение пропускной способности с помощью команды GetLastRequestStatistics в API MongoDB

API MongoDB поддерживает пользовательскую команду *getLastRequestStatistics*, которая используется, чтобы получить сведения о затратах единиц запроса для указанной операции.

Например, в оболочке Mongo выполните операцию, для которой необходимо проверить затраты единиц запроса.
```
> db.sample.find()
```

Затем выполните команду *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Исходя из этого, один из методов оценки необходимого для приложения объема зарезервированной пропускной способности — записать затраты единиц запросов на выполнение стандартных операций для определенного элемента, который используется приложением, а затем оценить предполагаемое количество операций в секунду.

> [!NOTE]
> Если размер и количество индексированных свойств для типов элемента существенно отличаются, запишите затраты единиц запросов для соответствующей операции, выполняемой с каждым *типом* стандартного элемента.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Получение пропускной способности с помощью метрик портала для API MongoDB

Чтобы без труда выполнить достоверную оценку затрат для единиц запроса для базы данных API MongoDB, используйте метрики [портала Azure](https://portal.azure.com). Используя диаграммы *Число запросов* и *Запросить оплату*, можно получить приблизительное количество единиц запроса, потребляемое каждой операцией, и количество единиц запроса, которое они потребляют относительно друг друга.

![Метрики портала API MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Превышение лимита зарезервированной пропускной способности в API MongoDB
Для приложений, потребляющих свыше выделенного объема пропускной способности для контейнера, пропускная способность будет ограничена до тех пор, пока показатель ее потребления не упадет ниже предельного уровня. В случае ограничения пропускной способности серверная часть будет заблаговременно завершать запрос с кодом ошибки `16500`: `Too Many Requests`. По умолчанию API MongoDB автоматически повторяет запрос до 10 раз, прежде чем вернуть код ошибки `Too Many Requests`. Если возникает много ошибок с кодом `Too Many Requests`, рекомендуется либо добавить логику повтора в процедуры обработки ошибок в своем приложении, либо [увеличить подготовленную пропускную способность контейнера](set-throughput.md).

## <a name="throughput-faq"></a>Часто задаваемые вопросы о пропускной способности

**Можно ли задать значение пропускной способности ниже 400 ЕЗ/с?**

400 ЕЗ/с — это минимальное значение пропускной способности, доступное для односекционных контейнеров Cosmos DB (минимальное значение для секционированных контейнеров — 1000 ЕЗ/с). Единицы запроса можно задать с интервалом в 100 ЕЗ/с, но невозможно задать значение пропускной способности равное 100 ЕЗ/с или любое значение менее 400 ЕЗ/с. Чтобы определить экономически эффективный метод разработки и тестирования в Cosmos DB, можно воспользоваться бесплатным [эмулятором Azure Cosmos DB](local-emulator.md), который развертывается локально и без дополнительных затрат. 

**Как настроить пропускную способность с использованием API MongoDB?**

Для настройки пропускной способности нет расширения API MongoDB. Рекомендуем использовать API SQL в соответствии с инструкциями в разделе о [настройке пропускной способности с помощью API SQL для .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о подготовке и глобальном масштабировании с помощью Cosmos DB см. в статье [Секционирование в базе данных Azure Cosmos DB с помощью API DocumentDB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png