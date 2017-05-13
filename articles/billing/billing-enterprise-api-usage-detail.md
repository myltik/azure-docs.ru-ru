---
title: "Интерфейсы API для выставления счетов Azure корпоративным клиентам: сведения об использовании | Документация Майкрософт"
description: "Сведения об интерфейсах API использования и RateCard для выставления счетов Azure, которые применяются для получения ценных сведений о потреблении ресурсов Azure и соответствующих тенденциях."
services: 
documentationcenter: 
author: aedwin
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: d6fe7a020100e3372c1fa0e244838d4c40839dbf
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---usage-details-preview"></a>Интерфейсы API отчетов для корпоративных клиентов: сведения об использовании (предварительная версия)

API сведений об использовании предоставляет сводку об израсходованных объемах и предполагаемых расходах для каждой регистрации с разбивкой по дням. Результаты также содержат сведения об экземплярах, метриках и отделах. Запрашивать данные в API можно по расчетному периоду или по дате начала и окончания. 
## <a name="consumption-apis"></a>Интерфейсы API потребления


##<a name="request"></a>Запрос 
Общие свойства заголовка, которые необходимо добавить, указываются [здесь](billing-enterprise-api.md). Если расчетный период не указан, то возвращаются данные за текущий расчетный период. Настраиваемые диапазоны времени можно указать с помощью параметров даты начала и окончания, которые вводятся в формате гггг-ММ-дд. Максимальный поддерживаемый диапазон времени — 36 месяцев.  

|Метод | URI запроса|
|-|-|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetails 
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>Ответ

> Из-за потенциально большого объема данных результирующий набор разбивается на страницы. Свойство nextLink (если имеется) указывает ссылку на следующую страницу данных. Если ссылка пустая, то это означает, что текущая страница является последней. 
<br/>

    {
        "id": "string",
        "data": [
            {                        
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "extendedCost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ]
        "nextLink": "string"
    }

<br/>

**Определения свойств ответа**

|Имя свойства| Тип| Описание
|-|-|-|
|id| string| Уникальный идентификатор для вызова API |
|data| Массив JSON| Массив сведений о ежедневном использовании для каждого экземпляра или каждой метрики|
|nextLink| string| При наличии нескольких страниц данных ссылка nextLink указывает на URL-адрес, возвращающий следующую страницу данных |

## <a name="see-also"></a>См. также
* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: расчетные периоды](billing-enterprise-api-billing-periods.md)

* [Интерфейсы API отчетов для корпоративных клиентов: баланс и сводка](billing-enterprise-api-balance-summary.md)

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: платежи в Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: прейскурант](billing-enterprise-api-pricesheet.md)
