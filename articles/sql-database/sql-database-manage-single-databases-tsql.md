---
title: "Создание и администрирование отдельных баз данных Azure SQL с помощью T-SQL | Документация Майкрософт"
description: "Краткий справочник по созданию и администрированию отдельных баз данных SQL Azure с помощью портала Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 951c88124219a6962f655b91275aec64c716b1cd
ms.lasthandoff: 03/28/2017


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>Создание и администрирование отдельных баз данных Azure SQL с помощью Transact-SQL

Вы можете создавать и администрировать отдельные базы данных SQL Azure с помощью [портала Azure](https://portal.azure.com/), PowerShell, Transact-SQL, REST API или языка C#. Этот раздел посвящен использованию портала Azure. PowerShell: см. дополнительные сведения о [создании и управлении отдельных баз данных с помощью PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). Transact-SQL: см. дополнительные сведения о [создании и администрировании отдельных баз данных с помощью Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>Создание базы данных SQL Azure с помощью Transact-SQL в SQL Server Management Studio

Вот как можно создать базу данных SQL Azure с помощью Transact-SQL в SQL Server Management Studio.

1. С помощью SQL Server Management Studio подключитесь к серверу базы данных Azure, используя имя для входа субъекта уровня сервера или имя для входа, которое является участником роли **dbmanager**. Дополнительные сведения об именах для входа см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).
2. В обозревателе объектов откройте узел "Базы данных", разверните папку **Системные базы данных**, щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.
3. Используйте инструкцию **CREATE DATABASE**, чтобы создать базу данных. Дополнительные сведения см. в статье [CREATE DATABASE (база данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx). Инструкция, приведенная ниже, создает базу данных **myTestDB** и указывает, что это база данных выпуска "Стандартный" S0 с максимальным размером 250 ГБ (по умолчанию).
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. Нажмите кнопку **Выполнить** для выполнения запроса.
5. В обозревателе объектов щелкните правой кнопкой мыши узел "Базы данных" и выберите **Обновить**, чтобы отобразить новую базу данных. 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Изменение уровня служб и уровня производительности отдельной базы данных
Измените максимальный размер базы данных с помощью [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx).

> [!TIP]
> Transact-SQL: см. дополнительные сведения о [создании баз данных с помощью Transact-SQL](sql-database-get-started.md).
>

## <a name="next-steps"></a>Дальнейшие действия
* Обзор средств управления см. в статье [Обзор: средства управления для Базы данных SQL](sql-database-manage-overview.md).
* Чтобы узнать, как выполнять задачи управления с использованием портала Azure, ознакомьтесь со статьей [Управление базами данных SQL Azure с помощью портала Azure](sql-database-manage-portal.md).
* Чтобы узнать, как выполнять задачи управления с использованием PowerShell, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью PowerShell](sql-database-manage-powershell.md).
* Чтобы узнать, как выполнять задачи управления с использованием SQL Server Management Studio, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Сведения о службе Базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). 
* Сведения о серверах и функциях базы данных Azure см. в статье [Возможности базы данных SQL Azure](sql-database-features.md).

