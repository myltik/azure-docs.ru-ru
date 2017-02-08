---
title: "Создание серверов базы данных SQL Azure | Документация Майкрософт"
description: "Краткий справочник по созданию серверов базы данных SQL Azure с помощью портала Azure и PowerShell."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: e8fe805da2446895b6616926fe1326dc30b9d0d0


---

# <a name="create-azure-sql-database-servers"></a>Создание серверов базы данных SQL Azure

Вы можете создать сервер базы данных SQL Azure с помощью [портала Azure](https://portal.azure.com/), PowerShell, REST API или языка C#. 

## <a name="create-an-azure-sql-database-server-using-the-azure-portal"></a>Создание сервера базы данных SQL Azure с помощью портала Azure

1. На [портале Azure](https://portal.azure.com/) откройте вкладку **Серверы SQL Server**. 

    ![Серверы SQL Server](./media/sql-database-get-started/new-sql-server.png)

2. Щелкните **Добавить**, чтобы создать сервер SQL Server.

    ![Добавление нового сервера SQL Server](./media/sql-database-get-started/new-sql-server-add.png)

> [!TIP]
> Чтобы приступить к работе с порталом Azure и SQL Server Management Studio, ознакомьтесь со статьей [Начало работы с серверами баз данных SQL Azure, базами данных и правилами брандмауэра с использованием портала Azure и SQL Server Management Studio](sql-database-get-started.md).
>

## <a name="create-an-azure-sql-database-server-using-powershell"></a>Создание сервера базы данных SQL Azure с помощью PowerShell

Чтобы создать сервер базы данных SQL, используйте командлет [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver). Замените *server1* именем своего сервера. Имена серверов должны быть уникальными по всем серверам базы данных SQL Azure. Если имя сервера уже используется, отобразится сообщение об ошибке. Выполнение этой команды может занять несколько минут. В подписке уже должна существовать группа ресурсов.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

> [!TIP]
> Пример сценария см. в разделе [Создание базы данных SQL с помощью сценария PowerShell](sql-database-get-started-powershell.md).
>

## <a name="additional-resources"></a>Дополнительные ресурсы
* Обзор средств управления см. в статье [Обзор: средства управления для Базы данных SQL](sql-database-manage-overview.md).
* Чтобы узнать, как выполнять задачи управления с использованием портала Azure, ознакомьтесь со статьей [Управление базами данных SQL Azure с помощью портала Azure](sql-database-manage-portal.md).
* Чтобы узнать, как выполнять задачи управления с использованием PowerShell, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью PowerShell](sql-database-manage-powershell.md).
* Чтобы узнать, как выполнять дополнительные задачи с использованием SQL Server Management Studio, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Сведения о службе Базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). 
* Сведения о серверах и функциях базы данных Azure см. в статье [Возможности базы данных SQL Azure](sql-database-features.md).



<!--HONumber=Dec16_HO3-->


