---
title: "Цена и производительности пула эластичных БД SQL"
description: "Сведения о ценах на пулы эластичных баз данных."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: e8d64ce5-e30a-445d-8c18-35b3825c6e76
ms.service: sql-database
ms.custom: sharded databases pool
ms.devlang: NA
ms.date: 05/27/2016
ms.author: srinia
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4e4afe2a4f5f3a06696ec0044368f272708667a0


---
# <a name="elastic-database-pool-billing-and-pricing-information"></a>Информация о выставлении счетов и ценах на пул эластичных баз данных
Счета по эластичным пулам баз данных выставляются в соответствии со следующими характеристиками:

* Счет по эластичному пулу выставляется после его создания, даже если в нем нет баз данных.
* Оплата за эластичный пул является почасовой. Она совпадает с частотой измерения уровней производительности отдельных баз данных.
* Если размер пула эластичных БД изменен на новое значение eDTU, то оплата пула согласно этому новому значению eDTU начисляется только после завершения операции по изменению размера. Используется та же схема, как при изменении уровня производительности автономных баз данных.
* Цена пула эластичных БД основывается на количестве eDTU пула. Стоимость пула эластичных БД не зависит от числа эластичных баз данных в нем и их использования.
* Цена вычисляется по формуле: (количество eDTU пула) x (цена за единицу eDTU).

Цена за единицу eDTU для пула эластичных БД выше, чем цена за единицу eDTU для автономной базы данных того же уровня служб. Подробнее об этом можно узнать в статье [Стоимость использования Базы данных SQL](https://azure.microsoft.com/pricing/details/sql-database/). 

Описание единиц eDTU и уровней служб см. в статье [Уровни служб базы данных SQL для отдельных баз данных и пулов эластичных баз данных](sql-database-service-tiers.md).

## <a name="next-steps"></a>Дальнейшие действия
* [Создание пула эластичных баз данных](sql-database-elastic-pool-create-portal.md)
* [Мониторинг, управление и размер пула эластичных баз данных](sql-database-elastic-pool-manage-portal.md)
* [Параметры и производительность баз данных SQL: возможности разных уровней службы](sql-database-service-tiers.md)
* [Сценарий PowerShell для идентификации баз данных, подходящих для пула эластичных баз данных](sql-database-elastic-pool-database-assessment-powershell.md)




<!--HONumber=Nov16_HO3-->


