---
title: "Azure PowerShell. Создание отдельной базы данных SQL | Документация Майкрософт"
description: "Вы узнаете, как создать логический сервер базы данных SQL, правило брандмауэра на уровне сервера и базы данных на портале Azure,"
keywords: "руководство по базам данных SQL, создание базы данных SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fe527f7de573b87fbc644cb6d71ae13816bc284b
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Создание отдельной базы данных SQL Azure с помощью PowerShell

PowerShell используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В данном руководстве подробно описывается, как с помощью PowerShell развернуть базу данных SQL Azure в группе ресурсов Azure на логическом сервере базы данных SQL.

Перед началом работы убедитесь, что установлена последняя версия PowerShell. Также должен быть установлен Azure CLI. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). 

## <a name="log-in-to-azure"></a>Вход в Azure

С помощью команды [Add-AzureRmAccount](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) войдите в подписку Azure и следуйте инструкциям на экране.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Создание логического сервера

Создайте логический сервер базы данных SQL с помощью команды [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). В примере ниже показано создание сервера со случайным именем в группе ресурсов с именем администратора `ServerAdmin` и паролем `ChangeYourAdminPassword1`. Замените эти предопределенные значения по своему усмотрению.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте правило брандмауэра на уровне сервера базы данных SQL с помощью команды [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как SQL Server Management Studio или программе sqlcmd, подключаться к базе данных SQL через брандмауэр службы базы данных SQL. В примере ниже показано создание правила брандмауэра для предопределенного диапазона адресов, который в этом примере представляет наиболее полный диапазон IP-адресов. Замените эти предопределенные значения значением внешнего IP-адреса или диапазона IP-адресов. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Создание пустой базы данных

С помощью команды [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) создайте на сервере пустую базу данных SQL с уровнем производительности S0. В примере ниже показано создание базы данных с именем `mySampleDatabase`. Замените это предопределенное значение по своему усмотрению.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные в этом кратком руководстве, выполните следующую команду:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о подключении к базе данных SQL с помощью SQL Server Management Studio и выполнении запроса к ней см. [здесь](sql-database-connect-query-ssms.md).
- Дополнительные сведения о подключении с помощью Visual Studio см. в статье [Azure SQL Database: Use Visual Studio Code to connect and query data](sql-database-connect-query.md) (База данных SQL Azure. Использование кода Visual Studio для подключения к базе данных и выполнения запроса).
* Технический обзор базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).

