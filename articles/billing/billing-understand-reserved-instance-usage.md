---
title: Выставление счетов в Azure. Использование зарезервированного экземпляра виртуальной машины Azure для подписки с оплатой по мере использования | Документация Майкрософт
description: Сведения о том, как оценить данные об использовании, чтобы составить представление о применении зарезервированного экземпляра виртуальной машины Azure для подписки с оплатой по мере использования.
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
ms.openlocfilehash: 7bf4aea86d4d430c15d60a8d73365705ace18b5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301858"
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Использование зарезервированного экземпляра виртуальной машины для подписки с оплатой по мере использования

Научитесь оценивать данные об использовании зарезервированного экземпляра виртуальной машины Azure с помощью ReservationId на [странице резервирования](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) и файла использования на [портале учетных записей Azure](https://account.azure.com).


>[!NOTE]
>Эта статья не относится к клиентам с соглашением Enterprise (EA). Если вы клиент с соглашением Enterprise, см. [общие сведения об использовании зарезервированных экземпляров с соглашением о регистрации Enterprise](billing-understand-reserved-instance-usage-ea.md). В этой статье также предполагается, что зарезервированный экземпляр применяется к одной подписке. Если зарезервированный экземпляр применяется к нескольким подпискам, он может покрывать использование нескольких CSV-файлов. 

В следующем разделе предполагается, что запущена виртуальная машина Windows Standard_DS1_v2 в регионе "Восточная часть США", а данные зарезервированного экземпляра соответствуют содержащимся в таблице ниже.

| Поле | Значение |
|---| :---: |
|Идентификатор резервирования |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Количество |1|
|SKU | Standard_DS1_v2|
|Регион | eastus |

## <a name="reserved-instance-application"></a>Применение зарезервированного экземпляра

Затраты на оборудование виртуальной машины покрываются, так как развернутая виртуальная машина соответствует атрибутам зарезервированного экземпляра. Чтобы узнать, какое программное обеспечение Windows не покрывается зарезервированным экземпляром, см. статью о [затратах на программное обеспечение Windows на зарезервированных экземплярах виртуальных машин](billing-reserved-instance-windows-software-costs.md).

### <a name="statement-section-of-csv"></a>Раздел Statement (Выписка) в CSV-файле
В этом разделе CSV-файла отображаются данные общего использования вашего зарезервированного экземпляра. Примените фильтр Reservation (Резервирование) к полю Meter Subcategory (Подкатегория измерения). Ваши данные будут выглядеть, как на приведенном ниже снимке экрана: ![Снимок экрана отфильтрованных данных об использовании и стоимости зарезервированного экземпляра](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Строка Reservation-Base VM (Зарезервированная базовая виртуальная машина) содержит общее количество часов, которые покрывает зарезервированный экземпляр. Эта строка содержит сумму 0,00 долларов США, так как зарезервированный экземпляр виртуальной машины покрывает расходы на нее. Строка Reservation-Windows Svr (1 Core) (Резервирование — Windows Svr (1 ядро)) покрывает стоимость программного обеспечения Windows.

### <a name="daily-usage-section-of-csv"></a>Раздел Daily usage (Ежедневное использование) в CSV-файле
Отфильтруйте дополнительные сведения и введите свой **идентификатор резервирования**. На следующем снимке экрана показаны поля, связанные с зарезервированным экземпляром. 

![Снимок экрана сведений об ежедневном использовании и расходах](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** в поле "Дополнительные сведения" представляет зарезервированный экземпляр, который был использован для применения преимущества к виртуальной машине.
2. ConsumptionMeter (Единица измерения потребления) представляет значение MeterId (Идентификатор единицы измерения) виртуальной машины.
3. В строке Reservation-Base VM (Зарезервированная базовая виртуальная машина) поля подкатегории измерения представляет строку затрат со значением 0 долларов США в разделе выписки. Затраты на работу этой виртуальной машины уже покрыты зарезервированным экземпляром.
4. Это идентификатор единицы измерения для зарезервированного экземпляра. Стоимость этой единицы измерения — 0 долларов США. Все виртуальные машины, для которых можно зарезервировать экземпляры, снабжены такими идентификаторами в CSV-файле, чтобы они учитывались при выставлении счетов. 
5. Виртуальная машина Standard_DS1_v2 с одним виртуальным процессором развертывается без преимущества гибридного использования Azure. Таким образом, эта единица измерения включает в себя дополнительную плату за программное обеспечение Windows. Ознакомьтесь с разделом [Windows software costs not included with Reserved Instances](billing-reserved-instance-windows-software-costs.md) (Затраты на программное обеспечение Windows в зарезервированных экземплярах виртуальных машин Azure). В ней вы найдете единицу измерения для 1-ядерной виртуальной машины серии D. Если применяется преимущество гибридного использования Azure, эта дополнительная плата не будет взиматься. 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о зарезервированных экземплярах см. в следующих статьях.

- [Сокращение затрат с помощью зарезервированных экземпляров виртуальных машин Azure](billing-save-compute-costs-reservations.md)
- [Предоплата за виртуальные машины с зарезервированными экземплярами](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Управление зарезервированными экземплярами виртуальных машин](billing-manage-reserved-vm-instance.md)
- [Сведения о применении скидки на зарезервированный экземпляр виртуальной машины](billing-understand-vm-reservation-charges.md)
- [Общие сведения об использовании зарезервированных экземпляров с Соглашением о регистрации Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Затраты на программное обеспечение Windows, которые не включены в стоимость зарезервированных экземпляров](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
