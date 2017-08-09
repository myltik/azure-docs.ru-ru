---
title: "Интерфейсы API для выставления счетов Azure корпоративным клиентам: баланс и сводка | Документация Майкрософт"
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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: ru-ru
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>API-интерфейсы отчетов для корпоративных клиентов: баланс и сводка

API-интерфейс для управления балансом предоставляет ежемесячную сводку о состоянии баланса, новых покупках, расходах на службы Azure Marketplace, корректировках и взимании платы за превышение.


##<a name="request"></a>Запрос 
Общие свойства заголовка, которые необходимо добавить, указываются [здесь](billing-enterprise-api.md). Если расчетный период не указан, то возвращаются данные за текущий расчетный период.

|Метод | URI запроса|
|-|-|
|ПОЛУЧЕНИЕ| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|ПОЛУЧЕНИЕ| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|

> [!Note]
> Чтобы использовать предварительную версию API, измените v2 на v1 в URL-адресе выше.
>

## <a name="response"></a>Ответ

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


**Определения свойств ответа**

|Имя свойства| Тип| Описание
|-|-|-|
|id|string|Уникальный идентификатор для определенного расчетного периода и регистрации|
|billingPeriodId|string |Идентификатор расчетного периода|
|currencyCode|string |Код валюты|
|beginningBalance|decimal| Начальный баланс для расчетного периода|
|endingBalance|decimal| Конечное сальдо для расчетного периода (для открытых периодов это значение обновляется ежедневно)|
|newPurchases|decimal| Общая сумма новой покупки|
|adjustments|decimal| Общая сумма корректировки|
|utilized|decimal| Общая сумма обязательств|
|serviceOverage|decimal| Превышение использования служб Azure|
|chargesBilledSeparately|decimal| Счета, выставляемые отдельно|
|totalOverage|decimal| serviceOverage + chargesBilledSeparately|
|totalUsage|decimal| Обязательства по службам Azure + totalOverage|
|azureMarketplaceServiceCharges|decimal| Общая сумма платежей за использование Azure Marketplace|
|newPurchasesDetails|Массив строк JSON из пар "имя-значение"|Список новых покупок|
|adjustmentDetails|Массив строк JSON из пар "имя-значение"|Список корректировок (акционный кредит, кредит SIE и т. д.) |


<br/>
## <a name="see-also"></a>См. также

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: расчетные периоды](billing-enterprise-api-billing-periods.md)

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: сведения об использовании](billing-enterprise-api-usage-detail.md) 

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: платежи в Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: прейскурант](billing-enterprise-api-pricesheet.md)
