---
title: Восстановление хранилища данных SQL Azure с помощью REST API | Документация Майкрософт
description: Здесь описывается восстановление хранилища данных SQL Azure с помощью REST API.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e1874fdf7c11d98d369072739c5937caffe6e96
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524430"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Восстановление хранилища данных SQL Azure с помощью интерфейсов REST API
Здесь описывается восстановление хранилища данных SQL Azure с помощью REST API.

## <a name="before-you-begin"></a>Перед началом работы
**Проверьте ресурсы DTU.** Каждое хранилище данных SQL размещается на логическом сервере SQL Server (например, myserver.database.windows.net), которому выделена [квота DTU](../sql-database/sql-database-what-is-a-dtu.md) по умолчанию.  Перед восстановлением хранилища данных SQL убедитесь, что у сервера SQL Server осталась достаточная квота DTU для восстанавливаемой базы данных. Чтобы запросить дополнительные единицы DTU, вы можете [создать запрос в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Восстановление активного или приостановленного хранилища данных
Чтобы восстановить хранилище данных, сделайте следующее:

1. Получите список точек восстановления базы данных с помощью операции Get Database Restore Points.
2. Начните восстановление с помощью операции [Create Database Restore Request](https://msdn.microsoft.com/library/azure/dn509571.aspx) .
3. Отследите состояние восстановления с помощью операции [Database Operation Status](https://msdn.microsoft.com/library/azure/dn720371.aspx) .

> [!NOTE]
> Чтобы настроить хранилище данных после восстановления, см. раздел [Настройка базы данных после восстановления](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Восстановление хранилища данных после удаления
Чтобы восстановить хранилище данных после удаления, сделайте следующее:

1. Создайте список всех удаленных хранилищ данных, доступных для восстановления, с помощью операции [List Restorable Dropped Databases](https://msdn.microsoft.com/library/azure/dn509562.aspx).
2. Получите подробные сведения об удаленном хранилище данных, которое необходимо восстановить, с помощью операции [Get restorable dropped database][Get restorable dropped database].
3. Начните восстановление с помощью операции [Create Database Restore Request](https://msdn.microsoft.com/library/azure/dn509571.aspx) .
4. Отследите состояние восстановления с помощью операции [Database Operation Status](https://msdn.microsoft.com/library/azure/dn720371.aspx) .

> [!NOTE]
> Чтобы настроить хранилище данных после восстановления, см. раздел [Настройка базы данных после восстановления](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о функциях, обеспечивающих непрерывность бизнес-процессов в выпусках базы данных SQL Azure, см. в статье [Обзор непрерывности бизнес-процессов в базе данных SQL Azure](../sql-database/sql-database-business-continuity.md).
