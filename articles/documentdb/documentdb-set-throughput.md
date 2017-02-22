---
title: "Подготовка пропускной способности для Azure DocumentDB | Документация Майкрософт"
description: "Узнайте, как настроить подготовленную пропускную способность для коллекции DocumentDB."
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: c65c4021f5d0beda8005abdd375ac51729acf0a8
ms.openlocfilehash: 8ff246ab7d70bd00ce989d3049e016d692722d18


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>Настройка пропускной способности для коллекций Azure DocumentDB

Пропускную способность для коллекций DocumentDB можно настроить на портале Azure или с помощью клиентских пакетов SDK. 

В следующей таблице указана пропускная способность, доступная для каждой коллекции.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Односекционная коллекция</strong></p></td>
            <td valign="top"><p><strong>Секционированная коллекция</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Минимальная пропускная способность</p></td>
            <td valign="top"><p>400 единиц запроса в секунду</p></td>
            <td valign="top"><p>2500 единиц запроса в секунду</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Максимальная пропускная способность</p></td>
            <td valign="top"><p>10&000; единиц запроса в секунду</p></td>
            <td valign="top"><p>Без ограничений</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE] 
> Чтобы указать для секционированных коллекций значение пропускной способности от 2500 до 10 000 ЕЗ в секунду, необходимо временно использовать портал Azure. Эта возможность пока не доступна в пакете SDK.

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Настройка пропускной способности с помощью портала Azure

1. В новом окне откройте [портал Azure](https://portal.azure.com).
2. На панели слева щелкните **NoSQL (DocumentDB)** или выберите внизу пункт **Больше служб**, перейдите к разделу **Базы данных** и выберите **NoSQL (DocumentDB)**.
3. Выберите учетную запись DocumentDB.
4. В новом окне в разделе **Коллекции** щелкните **Масштаб**, как показано на снимке экрана ниже.
5. В новом окне выберите коллекцию из раскрывающегося списка, измените значение **пропускной способности**, а затем нажмите кнопку **Сохранить**.

    ![Снимок экрана, показывающий, как изменить пропускную способность для коллекции на портале Azure, перейдя к учетной записи и выбрав "Масштаб"](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>Настройка пропускной способности с помощью пакета SDK для .NET

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о подготовке и глобальном масштабировании с помощью DocumentDB см. в статье [Секционирование и масштабирование в Azure DocumentDB](documentdb-partition-data.md).


<!--HONumber=Feb17_HO2-->


