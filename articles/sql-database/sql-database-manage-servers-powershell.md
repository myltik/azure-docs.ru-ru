---
title: "Создание и администрирование серверов баз данных SQL Azure с помощью PowerShell | Документация Майкрософт"
description: "Краткий справочник по созданию и администрированию серверов баз данных SQL Azure с помощью PowerShell."
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
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 835702c8381fcd184b056c76054514348c675bbf
ms.openlocfilehash: 9f1905f7e5aad0e43d81f7095089b9f3492a82ea


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>Создание и администрирование серверов баз данных SQL Azure с помощью PowerShell

Вы можете создавать и администрировать серверы баз данных SQL Azure с помощью [портала Azure](https://portal.azure.com/), PowerShell, REST API или языка C#. Этот раздел посвящен использованию PowerShell. Портал Azure: см. дополнительные сведения о [создании и администрировании серверов с помощью портала Azure](sql-database-manage-servers-portal.md). 

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
> Приме скрипта: см. дополнительные сведения о [создании баз данных SQL с помощью скрипта PowerShell](sql-database-get-started-powershell.md).
>

## <a name="additional-resources"></a>Дополнительные ресурсы
* Обзор средств управления см. в статье [Обзор: средства управления для Базы данных SQL](sql-database-manage-overview.md).
* Чтобы узнать, как выполнять задачи управления с использованием портала Azure, ознакомьтесь со статьей [Управление базами данных SQL Azure с помощью портала Azure](sql-database-manage-portal.md).
* Чтобы узнать, как выполнять задачи управления с использованием PowerShell, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью PowerShell](sql-database-manage-powershell.md).
* Чтобы узнать, как выполнять дополнительные задачи с использованием SQL Server Management Studio, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Сведения о службе Базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). 
* Сведения о серверах и функциях базы данных Azure см. в статье [Возможности базы данных SQL Azure](sql-database-features.md).



<!--HONumber=Feb17_HO1-->


