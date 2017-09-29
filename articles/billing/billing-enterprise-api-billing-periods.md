---
title: "Интерфейсы API для выставления счетов Azure корпоративным клиентам: расчетные периоды | Документация Майкрософт"
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
ms.openlocfilehash: c6880b79189e0683387a7aafbd6fa4805b3b42ef
ms.contentlocale: ru-ru
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---billing-periods"></a>Интерфейсы API отчетов для корпоративных клиентов: расчетные периоды

API расчетных периодов возвращает список расчетных периодов, которые содержат данные о потреблении для определенной регистрации, приведенные в обратном хронологическом порядке. Каждый период содержит свойство, указывающее на маршрут API к четырем наборам данных: BalanceSummary, UsageDetails, MarketplaceCharges и PriceSheet. Если период не содержит данных, то соответствующее свойство имеет значение null. 


##<a name="request"></a>Запрос 
Общие свойства заголовка, которые необходимо добавить, указываются [здесь](billing-enterprise-api.md). 

|Метод | URI запроса|
|-|-|
|ПОЛУЧЕНИЕ| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingperiods|

> [!Note]
> Чтобы использовать предварительную версию API, измените v2 на v1 в приведенном выше URL-адресе.
>

## <a name="response"></a>Ответ
 
    
    
      [
            {
                "billingPeriodId": "201704",
                "billingStart": "2017-04-01T00:00:00Z",
                "billingEnd": "2017-04-30T11:59:59Z",
                "balanceSummary": "/v1/enrollments/100/billingperiods/201704/balancesummary",
                "usageDetails": "/v1/enrollments/100/billingperiods/201704/usagedetails",
                "marketplaceCharges": "/v1/enrollments/100/billingperiods/201704/marketplacecharges",
                "priceSheet": "/v1/enrollments/100/billingperiods/201704/pricesheet"
            },          
            ....
      ]
    

**Определения свойств ответа**

|Имя свойства| Тип| Описание
|-|-|-|
|billingPeriodId| string| Уникальный идентификатор, представляющий определенный расчетный период|
|billingStart| datetime;| Строка в формате ISO 8601, представляющая дату начала периода|
|billingEnd| datetime;| Строка в формате ISO 8601, представляющая дату окончания периода|
|balanceSummary| string| URL-адрес, ведущий к сводным данным баланса за указанный период|
|usageDetails| string| URL-адрес, ведущий к сведениям об использовании за указанный период|
|marketplaceCharges| string| URL-адрес, ведущий к сведениям о расходах на заказы Marketplace за указанный период|
|priceSheet| string| URL-адрес, ведущий к данным прейскурантов за указанный период|

<br/>
## <a name="see-also"></a>См. также

* [Интерфейсы API отчетов для корпоративных клиентов: баланс и сводка](billing-enterprise-api-balance-summary.md)

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: сведения об использовании](billing-enterprise-api-usage-detail.md) 

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: платежи в Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: прейскурант](billing-enterprise-api-pricesheet.md)
