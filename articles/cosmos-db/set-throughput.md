---
title: "Подготовка пропускной способности для Azure Cosmos DB | Документация Майкрософт"
description: "Узнайте, как задать подготовленную пропускную способность для контейнеров, коллекций, графов и таблиц Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: mimig
ms.openlocfilehash: cf6eadbae328b1551da861fb5a11930ee830d415
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Настройка пропускной способности для коллекций Azure Cosmos DB

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
            <td valign="top"><p>1 000 единиц запросов в секунду</p></td>
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
2. На панели слева щелкните **Azure Cosmos DB** или выберите внизу пункт **Больше служб**, перейдите к разделу **Базы данных** и выберите **Azure Cosmos DB**.
3. Выберите учетную запись Cosmos DB.
4. В новом окне нажмите кнопку **обозреватель данных** в меню переходов.
5. В новом окне разверните узел базы данных и контейнера и щелкните **Scale & Settings** (Параметры масштабирования).
6. В новом окне в поле **Пропускная способность** введите новое значение пропускной способности, а затем щелкните **Сохранить**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Чтобы настроить пропускную способность с помощью API-Интерфейсы SQL для .NET

```C#
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Чтобы настроить пропускную способность с помощью API-Интерфейсы SQL для Java

Этот фрагмент кода взят из файла OfferCrudSamples.java [azure documentdb-java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) репозитория. 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 10300;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="throughput-faq"></a>Часто задаваемые вопросы о пропускной способности

**Можно ли задать значение пропускной способности ниже 400 ЕЗ/с?**

400 единиц Запросов в секунду имеет минимальную производительность в контейнерах одной секции Cosmos DB (минимальным для секционированных контейнеров является 1 000 единиц Запросов в секунду). Единицы запроса можно задать с интервалом в 100 ЕЗ/с, но невозможно задать значение пропускной способности равное 100 ЕЗ/с или любое значение менее 400 ЕЗ/с. Чтобы определить экономически эффективный метод разработки и тестирования в Cosmos DB, можно воспользоваться бесплатным [эмулятором Azure Cosmos DB](local-emulator.md), который развертывается локально и без дополнительных затрат. 

**Как настроить пропускную способность с использованием API MongoDB?**

Для настройки пропускной способности нет расширения API MongoDB. Рекомендуется использовать API-Интерфейсы SQL, как показано в [задание пропускную способность с помощью API-Интерфейсы SQL для .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о подготовке и глобальном масштабировании с помощью Cosmos DB см. в статье [Секционирование в базе данных Azure Cosmos DB с помощью API DocumentDB](partition-data.md).
