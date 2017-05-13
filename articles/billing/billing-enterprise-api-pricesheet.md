---
title: "Интерфейсы API для выставления счетов Azure корпоративным клиентам: прейскурант | Документация Майкрософт"
description: "Узнайте, как с помощью интерфейсов API отчетов для корпоративных клиентов Azure извлекать данные о потреблении программным способом."
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
ms.openlocfilehash: e5deb660ae5cfed5ac64000d070f4dd2e42ec9c0
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---pricesheet-preview"></a>Интерфейсы API отчетов для корпоративных клиентов: прейскурант (предварительная версия)

API прейскуранта предоставляет соответствующий тариф для каждой метрики в отдельной регистрации и за определенный расчетный период.

##<a name="request"></a>Запрос
Общие свойства заголовка, которые необходимо добавить, указываются [здесь](billing-enterprise-api.md). Если расчетный период не указан, то возвращаются данные за текущий расчетный период.

|Метод | URI запроса|
|-|-|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/pricesheet|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|


## <a name="response"></a>Ответ

    
          [
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                  "billingPeriodId": "201704",
                  "meterName": "A1 VM",
                  "unitOfMeasure": "100 Hours",
                  "includedQuantity": 0,
                  "partNumber": "N7H-00015",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                  "billingPeriodId": "201404",
                  "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                  "unitOfMeasure": "100 GB",
                  "includedQuantity": 0,
                  "partNumber": "N9H-00402",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            ...
        ]
    

**Определения свойств ответа**

|Имя свойства| Тип| Описание
|-|-|-|
|id| string| Уникальный идентификатор, представляющий определенный элемент PriceSheet (метрика за расчетный период)|
|billingPeriodId| string| Уникальный идентификатор, представляющий определенный расчетный период|
|meterName| string| Имя метрики|
|unitOfMeasure| string| Единица измерения для измерения службы|
|includedQuantity| decimal| Количество, которое включено |
|partNumber| string| Артикул, связанный со метрикой|
|unitPrice| decimal| Цена единицы измерения для метрики|
|currencyCode| string| Код валюты для unitPrice|
<br/>
## <a name="see-also"></a>См. также

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: расчетные периоды](billing-enterprise-api-billing-periods.md)

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: сведения об использовании](billing-enterprise-api-usage-detail.md)

* [Интерфейсы API отчетов для корпоративных клиентов: баланс и сводка](billing-enterprise-api-balance-summary.md)

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: платежи в Marketplace](billing-enterprise-api-marketplace-storecharge.md)

