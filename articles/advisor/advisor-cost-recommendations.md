---
title: "Рекомендации Azure Advisor по затратам | Документация Майкрософт"
description: "Использование Azure Advisor для оптимизации затрат на развернутые службы Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 39a882a8338ce820c6b9b98f58d1a9db7448c7be

---

# <a name="advisor-cost-recommendations"></a>Рекомендации Azure Advisor по затратам

Advisor помогает оптимизировать и уменьшить общие расходы на Azure, выявляя простаивающие и недостаточно нагруженные ресурсы. Рекомендации по затратам можно получить на вкладке **Стоимость** панели мониторинга Advisor.

![Вкладка "Стоимость" в Advisor](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="low-utilization-virtual-machines"></a>Недостаточно нагруженные виртуальные машины 

Хотя в некоторых сценариях приложение может намеренно обеспечивать низкую загрузку, часто можно сократить затраты, управляя размером и числом виртуальных машин. Advisor отслеживает использование виртуальных машин на протяжении 14 дней и определяет недостаточно нагруженные виртуальные машины. Виртуальные машины, у которых использование ресурсов ЦП составляет не больше 5 % и использование ресурсов сети составляет не больше 7 МБ на протяжении 4 или более дней, будут считаться недостаточно нагруженными.

Advisor показывает оценочную стоимость продолжения выполнения виртуальной машины. Вы можете завершить работу виртуальной машины или изменить ее размер.  

![Рекомендации Advisor по затратам на изменение размеров виртуальных машин](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="sql-elastic-database-pool-recommendations"></a>Рекомендации по пулу эластичных баз данных SQL

Advisor определяет экземпляры SQL Server, создание пулов эластичных баз данных для которых дает преимущества. Пулы эластичных баз данных обеспечивают простое и экономически выгодное решение для управления целевыми показателями производительности для нескольких баз данных с разными моделями использования. Дополнительные сведения о пулах эластичных баз данных Azure см. в статье [Что такое пул эластичных БД Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)

![Рекомендации Advisor по затратам на пулы эластичных баз данных](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Как получить доступ к рекомендациям по затратам в Azure Advisor

1. Войдите на [портал Azure](https://portal.azure.com).
2. В области навигации слева щелкните **Больше служб**, прокрутите вниз область меню служб до раздела **Мониторинг и управление** и щелкните **Помощник по Azure**. Откроется панель мониторинга Advisor. 
3. На панели мониторинга Advisor щелкните вкладку **Стоимость**, выберите подписку, для которой вы хотите получать рекомендации, и щелкните **Получить рекомендации**.

> [!NOTE]
> Azure Advisor создает рекомендации для подписок, для которых вам назначена роль **владельца**, **участника** или **читателя**.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о рекомендациях Advisor, ознакомьтесь с приведенными ниже материалами.
-  [Общие сведения об Azure Advisor](advisor-overview.md)
-  [Начало работы](advisor-get-started.md)
-  [Рекомендации Azure Advisor по высокой доступности](advisor-cost-recommendations.md)
-  [Рекомендации Azure Advisor по безопасности](advisor-cost-recommendations.md)
-  [Рекомендации Azure Advisor по производительности](advisor-cost-recommendations.md)



<!--HONumber=Nov16_HO3-->


