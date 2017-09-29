---
title: "Интерфейсы API для выставления счетов Azure корпоративным клиентам: платежи в Marketplace | Документация Майкрософт"
description: "Узнайте, как с помощью интерфейсов API отчетов для корпоративных клиентов Azure извлекать данные о потреблении программным способом."
services: 
documentationcenter: 
author: cwatson-cat
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 5539623f7ae35e14b6dafe6fdf9efe4bcaba4fd3
ms.contentlocale: ru-ru
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---marketplace-store-charge"></a>API-интерфейсы отчетов для корпоративных клиентов: платежи в Marketplace

Интерфейс API платежей в Marketplace предоставляет сводку о расходах в Marketplace с разбивкой по дням. Данные основаны на фактическом использовании и отображаются для указанного расчетного периода или дат начала и окончания (однократные сборы не включаются).

##<a name="request"></a>Запрос 
Общие свойства заголовка, которые необходимо добавить, указываются [здесь](billing-enterprise-api.md). Если расчетный период не указан, то возвращаются данные за текущий расчетный период. Настраиваемые диапазоны времени можно указать с помощью параметров даты начала и окончания, которые вводятся в формате гггг-ММ-дд. Максимальный поддерживаемый диапазон времени — 36 месяцев.  

|Метод | URI запроса|
|-|-|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacecharges|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Чтобы использовать предварительную версию API, измените v2 на v1 в URL-адресе выше.
>

## <a name="response"></a>Ответ
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

**Определения свойств ответа**

|Имя свойства| Тип| Описание
|-|-|-|
|id|string|Уникальный идентификатор для элемента платежа в Marketplace|
|subscriptionGuid|Guid|Идентификатор GUID подписки|
|subscriptionName|string|Имя подписки|
|meterId|string|Идентификатор для генерируемой метрики|
|usageStartDate|DateTime|Время начала записи использования|
|usageEndDate|DateTime|Время окончания записи использования|
|offerName|string|Название предложения|
|resourceGroup|string|Группа ресурсов|
|instanceId|string|Идентификатор экземпляра|
|additionalInfo|string|Строка JSON с дополнительной информацией|
|tags|string|Строка JSON с тегами|
|orderNumber|string|Порядковый номер|
|unitOfMeasure|string|Единица измерения метрики|
|costCenter|string|Место возникновения затрат|
|accountId|int|Идентификатор учетной записи|
|accountName|string |Имя учетной записи|
|accountOwnerId|string|Идентификатор владельца учетной записи|
|departmentId|int|Идентификатор отдела|
|departmentName|string|Название отдела|
|publisherName|string|Имя издателя|
|planName|string|Имя плана|
|consumedQuantity|decimal|Потребленный объем за этот период времени|
|resourceRate|decimal|Цена единицы для метрики|
|extendedCost|decimal|Оценка расходов на основе потребленного объема и расширенных затрат|
<br/>
## <a name="see-also"></a>См. также

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: расчетные периоды](billing-enterprise-api-billing-periods.md)

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: сведения об использовании](billing-enterprise-api-usage-detail.md) 

* [Интерфейсы API отчетов для корпоративных клиентов: баланс и сводка](billing-enterprise-api-balance-summary.md)

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: прейскурант](billing-enterprise-api-pricesheet.md)
