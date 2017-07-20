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
ms.date: 06/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: d541bb19ba7e5ecb44c9fe91b1e232d4d9c2170e
ms.contentlocale: ru-ru
ms.lasthandoff: 06/13/2017


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
            <td valign="top"><p>2500 единиц запроса в секунду</p></td>
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
4. В новом окне в меню навигации щелкните **Data Explorer (Preview)** (Обозреватель данных (предварительная версия)).
5. В новом окне разверните узел базы данных и контейнера и щелкните **Scale & Settings** (Параметры масштабирования).
6. В новом окне в поле **Пропускная способность** введите новое значение пропускной способности, а затем щелкните **Сохранить**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-documentdb-api-for-net"></a>Настройка пропускной способности с помощью DocumentDB API для .NET

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>Часто задаваемые вопросы о пропускной способности

**Можно ли задать значение пропускной способности ниже 400 ЕЗ/с?**

400 ЕЗ/с — это минимальное значение пропускной способности, доступное для односекционных коллекций Cosmos DB (минимальное значение для секционированных коллекций — 2500 ЕЗ/с). Единицы запроса можно задать с интервалом в 100 ЕЗ/с, но невозможно задать значение пропускной способности равное 100 ЕЗ/с или любое значение менее 400 ЕЗ/с. Чтобы определить экономически эффективный метод разработки и тестирования в Cosmos DB, можно воспользоваться бесплатным [эмулятором Azure Cosmos DB](local-emulator.md), который развертывается локально и без дополнительных затрат. 

**Как настроить пропускную способность с использованием API MongoDB?**

Для настройки пропускной способности нет расширения API MongoDB. Мы рекомендуем использовать DocumentDB API, как показано в разделе [Настройка пропускной способности с помощью API DocumentDB для .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о подготовке и глобальном масштабировании с помощью Cosmos DB см. в статье [Секционирование в базе данных Azure Cosmos DB с помощью API DocumentDB](partition-data.md).

