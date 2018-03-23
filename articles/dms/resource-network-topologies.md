---
title: Сетевые топологии для переноса Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service | Документация Майкрософт
description: Сведения об исходной и целевой конфигурациях для Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 892cff02b5b70f09236bb37ae786f180ddca9316
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Сетевые топологии для переноса Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service
Из этой статьи вы узнаете о различных сетевых топологиях, с которыми может работать Azure Database Migration Service для бесперебойного переноса Управляемого экземпляра Базы данных SQL Azure из локальных систем SQL Server.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Управляемый экземпляр Базы данных SQL Azure, настроенный для гибридных рабочих нагрузок 
Используйте эту топологию, если Управляемый экземпляр Базы данных SQL Azure подключен к локальной сети. Такой подход упрощает сетевую маршрутизацию и максимально повышает пропускную способность во время миграции.

![Сетевая топология для гибридных рабочих нагрузок](media\resource-network-topologies\hybrid-workloads.png)

**Требования**
- В этом сценарии создается Управляемый экземпляр Базы данных SQL Azure и экземпляр Azure Database Migration Service в одной виртуальной сети Azure, но в разных подсетях.  
- Виртуальная сеть, используемая в этом сценарии, также подключена к локальной сети с помощью ExpressRoute или VPN.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Управляемый экземпляр Базы данных SQL Azure, изолированный от локальной сети
Используйте эту сетевую топологию, если в вашей среде нужно применить один или несколько из следующих сценариев:
- Управляемый экземпляр Базы данных SQL Azure изолирован от локального подключения, но экземпляр Azure Database Migration Service подключен к локальной сети.
- Применяются политики управления доступом на основе ролей, и вы ограничиваете доступ пользователей к одной подписке, в которой размещен Управляемый экземпляр Базы данных SQL Azure.
- Виртуальные сети, используемые для Управляемого экземпляра Базы данных SQL Azure и для Azure Database Migration Service, находятся в разных подписках.

![Сетевая топология для Управляемого экземпляра Базы данных SQL Azure, изолированного от локальной сети](media\resource-network-topologies\mi-isolated-workload.png)

**Требования**
- Виртуальная сеть, которую использует Azure Database Migration Service для этого сценария, также должна быть подключена к локальной сети с помощью ExpressRoute или VPN.
- Создайте пиринг между виртуальными сетями, используемыми для Управляемого экземпляра Базы данных SQL Azure и для Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations"></a>Миграция между облаками
Используйте эту топологию, если исходная платформа SQL Server находится на виртуальной машине Azure.

![Сетевая топология для миграции между облаками](media\resource-network-topologies\cloud-to-cloud.png)

**Требования**
- Создайте пиринг между виртуальными сетями, используемыми для Управляемого экземпляра Базы данных SQL Azure и для Azure Database Migration Service.

## <a name="see-also"></a>См. также
- [Migrate SQL Server to Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) (Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure)
- [Предварительные требования для использования службы Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Создание виртуальной сети с помощью портала Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Дополнительная информация
Общие сведения о службе Azure Database Migration Service и доступности по регионам в период действия общедоступной предварительной версии см. в статье [Что такое предварительная версия службы Azure Database Migration Service?](dms-overview.md) 