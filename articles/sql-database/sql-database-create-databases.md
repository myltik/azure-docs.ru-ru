---
title: "Создание базы данных SQL Azure | Документация Майкрософт"
description: "Краткий справочник по созданию базы данных SQL Azure с помощью портала Azure, PowerShell или Transact-SQL."
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
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: aca33871d83a34769fd2b09be4b382c872a65f0a


---
# <a name="create-an-azure-sql-database"></a>Создание базы данных SQL Azure

Вы можете создать базу данных SQL Azure с помощью [портала Azure](https://portal.azure.com/), PowerShell, Transact-SQL, REST API или языка C#. 

## <a name="create-an-azure-sql-database-using-the-azure-portal"></a>Создание базы данных SQL Azure с помощью портала Azure

1. На [портале Azure](https://portal.azure.com/) откройте колонку **Базы данных SQL**. 

    ![базы данных sql](./media/sql-database-get-started/sql-databases.png)
2. В колонке баз данных SQL щелкните **Добавить**.

    ![Добавление базы данных SQL](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Чтобы приступить к работе с порталом Azure и SQL Server Management Studio, ознакомьтесь со статьей [Начало работы с серверами баз данных SQL Azure, базами данных и правилами брандмауэра с использованием портала Azure и SQL Server Management Studio](sql-database-get-started.md).
>

## <a name="create-an-azure-sql-database-using-powershell"></a>Создание базы данных SQL Azure с помощью PowerShell

Чтобы создать базу данных SQL, используйте командлет [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase). В подписке уже должны существовать группа ресурсов и сервер. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```
> [!TIP]
> Пример сценария см. в разделе [Создание базы данных SQL с помощью сценария PowerShell](sql-database-get-started-powershell.md).
>

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>Создание базы данных SQL Azure с помощью Transact-SQL в SQL Server Management Studio

Вот как можно создать базу данных SQL Azure с помощью Transact-SQL в SQL Server Management Studio.

1. С помощью SQL Server Management Studio подключитесь к серверу базы данных Azure, используя имя для входа субъекта уровня сервера или имя для входа, которое является участником роли **dbmanager**. Дополнительные сведения об именах для входа см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).
2. В обозревателе объектов откройте узел "Базы данных", разверните папку **Системные базы данных**, щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.
3. Используйте инструкцию **CREATE DATABASE**, чтобы создать базу данных. Дополнительные сведения см. в статье [CREATE DATABASE (база данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx). Инструкция, приведенная ниже, создает базу данных **myTestDB** и указывает, что это база данных выпуска "Стандартный" S0 с максимальным размером 250 ГБ (по умолчанию).
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. Нажмите кнопку **Выполнить** для выполнения запроса.
5. В обозревателе объектов щелкните правой кнопкой мыши узел "Базы данных" и выберите **Обновить**, чтобы отобразить новую базу данных. 

> [!TIP]
> Чтобы приступить к работе с порталом Azure и SQL Server Management Studio, ознакомьтесь со статьей [Начало работы с серверами баз данных SQL Azure, базами данных и правилами брандмауэра с использованием портала Azure и SQL Server Management Studio](sql-database-get-started.md).
>

## <a name="additional-resources"></a>Дополнительные ресурсы
* Обзор средств управления см. в статье [Обзор: средства управления для Базы данных SQL](sql-database-manage-overview.md).
* Чтобы узнать, как выполнять задачи управления с использованием портала Azure, ознакомьтесь со статьей [Управление базами данных SQL Azure с помощью портала Azure](sql-database-manage-portal.md).
* Чтобы узнать, как выполнять задачи управления с использованием PowerShell, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью PowerShell](sql-database-manage-powershell.md).
* Чтобы узнать, как выполнять задачи управления с использованием SQL Server Management Studio, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Сведения о службе Базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). 
* Сведения о серверах и функциях базы данных Azure см. в статье [Возможности базы данных SQL Azure](sql-database-features.md).



<!--HONumber=Dec16_HO3-->


