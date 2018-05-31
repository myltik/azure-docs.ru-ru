---
title: Выставление счетов в Azure. Общие сведения об использовании зарезервированных экземпляров Azure с соглашением о регистрации Enterprise | Документация Майкрософт
description: Сведения о том, как оценить данные об использовании, чтобы составить представление о применении зарезервированного экземпляра виртуальной машины Azure для соглашения о регистрации Enterprise.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301320"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Общие сведения об использовании зарезервированных экземпляров Azure с соглашением о регистрации Enterprise
Научитесь понимать данные об использовании зарезервированного экземпляра с помощью **ReservationId** со [страницы резервирования](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) и использовать файл с [портала EA](https://ea.azure.com). Вы можете также просмотреть данные об использовании зарезервированного экземпляра в разделе сводки на [портале EA](https://ea.azure.com).

>[!NOTE]
>Если вы приобрели зарезервированный экземпляр в контексте выставления счетов с оплатой по мере использования, обратитесь к разделу [Использование зарезервированного экземпляра виртуальной машины для подписки с оплатой по мере использования.](billing-understand-reserved-instance-usage.md)

В следующем разделе предполагается, что запущена виртуальная машина Windows Standard_D1_v2 в регионе "Восточная часть США", а данные зарезервированного экземпляра соответствуют содержащимся в таблице ниже.

| Поле | Значение |
|---| --- |
|Идентификатор резервирования |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Количество |1|
|SKU | Standard_D1|
|Регион | eastus |

## <a name="reserved-instance-application"></a>Применение зарезервированного экземпляра

Затраты на оборудование виртуальной машины покрываются, так как развернутая виртуальная машина соответствует атрибутам зарезервированного экземпляра. Чтобы узнать, какое программное обеспечение Windows не входит в состав зарезервированного экземпляра, перейдите к разделу c описанием [затрат на программное обеспечение Windows, не входящее в состав Azure Reserved VM Instances](billing-reserved-instance-windows-software-costs.md).


### <a name="reserved-instance-usage-in-csv"></a>Данные об использовании зарезервированного экземпляра в формате CSV
Можно скачать CSV-файл данных об использовании EA с портала EA. В скачанном CSV-файле отфильтруйте дополнительные сведения и введите свой **идентификатор резервирования**. На следующем снимке экрана показаны поля, связанные с зарезервированным экземпляром.

![Соглашения Enterprise (EA) для зарезервированного экземпляра Azure в формате CSV](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** в поле "Дополнительные сведения" представляет зарезервированный экземпляр, который был использован для применения преимущества к виртуальной машине.
2. ConsumptionMeter представляет собой значение MeterId виртуальной машины.
3. Это ReservationMeter без затрат, так как затраты на выполнение виртуальной машины уже оплачены зарезервированным экземпляром. 
4. Виртуальная машина Standard_D1 имеет один виртуальный ЦП и развертывается без использования преимущества гибридного использования Azure. Таким образом данная единица измерения включает в себя дополнительную плату за программное обеспечение Windows. Ознакомьтесь с разделом [Windows software costs not included with Reserved Instances](billing-reserved-instance-windows-software-costs.md) (Затраты на программное обеспечение Windows в зарезервированных экземплярах виртуальных машин Azure). В нем вы найдете единицу измерения для 1-ядерной виртуальной машины серии D. В случае применения преимущества гибридного использования Azure эта дополнительная плата не будет взиматься.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Данные об использовании зарезервированного экземпляра на странице сводки по использованию на портале EA

Данные об использовании зарезервированного экземпляра также отображаются в разделе сводки по использованию на портале EA: ![Сводка по использованию соглашения Enterprise](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png).

1. Плата за аппаратный компонент виртуальной машины не взимается, так как она входит в стоимость зарезервированного экземпляра. 
2. Взимается плата за программное обеспечение Windows, так как преимущество гибридного использования Azure не применяется. 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о зарезервированных экземплярах Azure см. в следующих статьях:

- [Сокращение затрат с помощью зарезервированных экземпляров виртуальных машин Azure](billing-save-compute-costs-reservations.md)
- [Предоплата за виртуальные машины с зарезервированными экземплярами](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Управление зарезервированными экземплярами виртуальных машин](billing-manage-reserved-vm-instance.md)
- [Сведения о применении скидки на зарезервированный экземпляр виртуальной машины](billing-understand-vm-reservation-charges.md)
- [Использование зарезервированного экземпляра виртуальной машины для подписки с оплатой по мере использования](billing-understand-reserved-instance-usage.md)
- [Затраты на программное обеспечение Windows, которые не включены в стоимость зарезервированных экземпляров](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.