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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: ru-ru
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>API-интерфейсы отчетов для корпоративных клиентов: сведения об использовании

API сведений об использовании предоставляет сводку об израсходованных объемах и предполагаемых расходах для каждой регистрации с разбивкой по дням. Результаты также содержат сведения об экземплярах, метриках и отделах. Запрашивать данные в API можно по расчетному периоду или по дате начала и окончания. 
## <a name="consumption-apis"></a>Интерфейсы API потребления


##<a name="request"></a>Запрос 
Общие свойства заголовка, которые необходимо добавить, указываются [здесь](billing-enterprise-api.md). Если расчетный период не указан, то возвращаются данные за текущий расчетный период. Настраиваемые диапазоны времени можно указать с помощью параметров даты начала и окончания, которые вводятся в формате гггг-ММ-дд. Максимальный поддерживаемый диапазон времени — 36 месяцев.  

|Метод | URI запроса|
|-|-|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|ПОЛУЧЕНИЕ|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Чтобы использовать предварительную версию API, замените v2 на v1 в приведенном выше URL-адресе.
>

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
            "Cost": 0,
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
        ],
        "nextLink": "string"
    }

<br/>
**Определения свойств ответа**

|Имя свойства| Тип| Описание
|-|-|-|
|id| string| Уникальный идентификатор для вызова API |
|data| Массив JSON| Массив сведений о ежедневном использовании для каждого экземпляра и каждого индикатора.|
|nextLink| string| Если данные разбиты на несколько страниц, ссылка nextLink указывает на URL-адрес следующей страницы данных. |
|accountId| int| Устаревшее поле. Сохранено для обратной совместимости. |
|productId| int| Устаревшее поле. Сохранено для обратной совместимости. |
|resourceLocationId| int| Устаревшее поле. Сохранено для обратной совместимости. |
|consumedServiceID| int| Устаревшее поле. Сохранено для обратной совместимости. |
|departmentId| int| Устаревшее поле. Сохранено для обратной совместимости. |
|accountOwnerEmail| string| Электронная почта владельца учетной записи. |
|accountName| string| Определяемое клиентом имя для учетной записи. |
|serviceAdministratorId| string| Адрес электронной почты администратора службы. |
|subscriptionId| int| Устаревшее поле. Сохранено для обратной совместимости. |
|subscriptionGuid| string| Глобальный уникальный идентификатор подписки. |
|subscriptionName| string| Имя подписки. |
|дата| string| Дата потребления. |
|product| string| Дополнительные сведения на индикаторе. Пример: виртуальная машина A1 Windows — восток Азиатско-тихоокеанского региона|
|meterId| string| Идентификатор индикатора, от которого получены данные об использовании. |
|meterCategory| string| Используемая служба платформы Azure. |
|meterSubCategory| string| Определяет тип службы Azure и может влиять на тариф. Пример: виртуальная машина A1 (не Windows)|
|meterRegion| string| В этом столбце указывается расположение центра обработки данных для определенных служб. От этого расположения может зависеть стоимость некоторых услуг. |
|meterName| string| Имя индикатора. |
|consumedQuantity| double| Объем использованных ресурсов по этому индикатору. |
|resourceRate| double| Тариф, применяемый к оплачиваемым единицам. |
|cost| double| Стоимость использованных ресурсов по этому индикатору. |
|resourceLocation| string| Центр обработки данных, в котором работает индикатор. |
|consumedService| string| Используемая служба платформы Azure. |
|instanceId| string| Содержит имя ресурса либо полный идентификатор ресурса. Подробные сведения см. на странице, посвященной [API Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|serviceInfo1| string| Метаданные внутренней службы Azure. |
|serviceInfo2| string| Например, тип образа для виртуальной машины и имя поставщика услуг Интернета для ExpressRoute. |
|additionalInfo| string| Метаданные определенных служб. Например, тип образа для виртуальной машины. |
|Теги| string| Добавляемые клиентом теги. Дополнительные сведения см. в статье [Использование тегов для организации ресурсов в Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags). |
|storeServiceIdentifier| string| Этот столбец не используется. Сохранен для обратной совместимости. |
|departmentName| string| Наименование подразделения. |
|costCenter| string| Место возникновения затрат по этому индикатору. |
|unitOfMeasure| string| Указывает единицу тарификации службы. Например: гигабайты, часы, десятки тысяч секунд. |
|resourceGroup| string| Группа ресурсов, в которой выполняется развернутое средство измерения. Дополнительные сведения см. в [обзоре Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
<br/>
## <a name="see-also"></a>См. также

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: расчетные периоды](billing-enterprise-api-billing-periods.md)

* [Интерфейсы API отчетов для корпоративных клиентов: баланс и сводка](billing-enterprise-api-balance-summary.md)

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: платежи в Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [Интерфейсы API для выставления счетов Azure корпоративным клиентам: прейскурант](billing-enterprise-api-pricesheet.md)

