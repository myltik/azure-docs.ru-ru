---
title: "Azure PowerShell. Создание базы данных SQL | Документация Майкрософт"
description: "Вы узнаете, как создать логический сервер базы данных SQL, правило брандмауэра на уровне сервера и базы данных на портале Azure,"
keywords: "руководство по базам данных SQL, создание базы данных SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: quickstart
ms.date: 04/17/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: d1acc548dca4c89572eece8dbdae0eae4853a97c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Создание отдельной базы данных SQL Azure с помощью PowerShell

PowerShell используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом руководстве подробно описывается, как с помощью PowerShell развернуть базу данных SQL Azure в [группе ресурсов Azure](../azure-resource-manager/resource-group-overview.md) на [логическом сервере базы данных Azure SQL](sql-database-features.md).

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 4.0. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Вход в Azure

С помощью команды [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) войдите в подписку Azure и следуйте инструкциям на экране.

```powershell
Add-AzureRmAccount
```

## <a name="create-variables"></a>Создание переменных

Определите переменные для использования в скриптах этого руководства.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure ](../azure-resource-manager/resource-group-overview.md) с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Создание логического сервера

Создайте [логический сервер базы данных Azure SQL](sql-database-features.md) с помощью команды [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver). Логический сервер содержит группу баз данных, которыми можно управлять как группой. В примере ниже показано создание сервера со случайным именем в группе ресурсов с именем администратора `ServerAdmin` и паролем `ChangeYourAdminPassword1`. Замените эти предопределенные значения по своему усмотрению.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте [правило брандмауэра на уровне сервера базы данных Azure SQL](sql-database-firewall-configure.md) с помощью команды [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как SQL Server Management Studio или программе sqlcmd, подключаться к базе данных SQL через брандмауэр службы базы данных SQL. В следующем примере брандмауэр открыт только для других ресурсов Azure. Чтобы включить возможность внешнего подключения, измените IP-адрес на соответствующий адрес своей среды. Чтобы открыть все IP-адреса, используйте 0.0.0.0 как начальный IP-адрес, а 255.255.255.255 — как конечный.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> База данных SQL обменивается данными через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу базы данных SQL Azure. Для этого ваш ИТ-отдел должен открыть порт 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Создание базы данных на сервере с образцами данных

С помощью команды [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) создайте на сервере базу данных с [уровнем производительности S0](sql-database-service-tiers.md). В следующем примере создается база данных с именем `mySampleDatabase`, в которую загружается образец данных AdventureWorksLT. При необходимости замените эти предопределенные значения (другие краткие руководства в этой коллекции созданы на основе этого документа).

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -SampleName "AdventureWorksLT" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие краткие руководства в этой коллекции созданы на основе этого документа. 

> [!TIP]
> Если вы планируете продолжать работу с этими краткими руководствами, не удаляйте созданные ресурсы. Если вы не планируете продолжать работу, удалите все созданные ресурсы, выполнив на портале Azure следующие действия.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть база данных, вы можете подключиться и создать запрос, используя привычные средства. См. дополнительные сведения о доступных средствах:

- [SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [Visual Studio Code](sql-database-connect-query-vscode.md)
- [.NET](sql-database-connect-query-dotnet.md)
- [PHP](sql-database-connect-query-php.md)
- [Node.js](sql-database-connect-query-nodejs.md)
- [Java](sql-database-connect-query-java.md)
- [Python](sql-database-connect-query-python.md)
- [Ruby](sql-database-connect-query-ruby.md)


