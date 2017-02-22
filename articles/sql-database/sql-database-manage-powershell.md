---
title: "Управление базой данных SQL Azure с помощью PowerShell | Документация Майкрософт"
description: "Управление базой данных SQL Azure с помощью PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 32d8c5f13d95c3de8b06782f4f6541866389be5b


---
# <a name="managing-azure-sql-database-using-powershell"></a>Управление базой данных SQL Azure с помощью PowerShell

В этой статье представлены командлеты PowerShell, которые используются для выполнения различных задач базы данных SQL Azure. Полный список командлетов базы данных SQL Azure см. [здесь](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx).

> [!TIP]
> Инструкции по созданию сервера, созданию брандмауэра на основе сервера, просмотру свойств сервера, подключению и отправке запросов к главной базе данных, созданию примера базы данных и пустой базы данных, запрашиванию свойств базы данных, подключению и отправке запросов к примеру базы данных см. в этом [руководстве по началу работы](sql-database-get-started-powershell.md).
>

## <a name="how-do-i-create-a-resource-group"></a>Создание группы ресурсов
Чтобы создать группу ресурсов для базы данных SQL и связанных ресурсов Azure, используйте командлет [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).
Полное руководство см. в статье [Создание базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server"></a>Создание сервера базы данных SQL
Чтобы создать сервер базы данных SQL, используйте командлет [New-AzureRmSqlServer](/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). Замените *server1* именем своего сервера. Имена серверов должны быть уникальными по всем серверам базы данных SQL Azure. Если имя сервера уже используется, отобразится сообщение об ошибке. Выполнение этой команды может занять несколько минут. В подписке уже должна существовать группа ресурсов.

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

Дополнительные сведения о серверах см. в статье о [функциях и компонентах базы данных SQL](sql-database-features.md). Полное руководство см. в статье [Создание базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>Создание правила брандмауэра для сервера базы данных SQL
Чтобы создать правило брандмауэра для доступа к серверу, используйте командлет [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). Выполните следующую команду, заменив начальный и конечный IP-адреса значениями для своего клиента. В подписке уже должны существовать группа ресурсов и сервер.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Чтобы другие службы Azure могли иметь доступ к серверу, создайте правило брандмауэра и задайте для адресов `-StartIpAddress` и `-EndIpAddress` значение **0.0.0.0**. Это специальное правило брандмауэра разрешит всему трафику Azure доступ к серверу.

Дополнительные сведения см. в статье [Брандмауэр базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Полное руководство см. в статье [Создание базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database"></a>Создание базы данных SQL
Чтобы создать базу данных SQL, используйте командлет [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx). В подписке уже должны существовать группа ресурсов и сервер. 

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

Дополнительные сведения см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). Полное руководство см. в статье [Создание базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>Изменение уровня производительности базы данных SQL
Чтобы изменить масштаб базы данных, используйте командлет [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx). В подписке уже должны существовать группа ресурсов, сервер и база данных. Задайте параметру `-RequestedServiceObjectiveName` единое пространство (как показано в следующем фрагменте кода) для категории "Базовый". Присвойте ему значения *S0*, *S1*, *P1*, *P6* и т. д., как в предыдущем примере для других уровней.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Дополнительные сведения см. в статье [Параметры базы данных SQL и производительность: возможности разных уровней служб](sql-database-service-tiers.md). Пример сценария см. в разделе [Пример скрипта PowerShell для изменения уровня обслуживания и уровня производительности базы данных SQL](sql-database-manage-single-databases-powershell.md#change-the-service-tier-and-performance-level-of-a-single-database).

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>Копирование базы данных SQL на тот же сервер
Чтобы скопировать базу данных SQL на тот же сервер, используйте командлет [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx). Параметрам `-CopyServerName` и `-CopyResourceGroupName` задайте значения, соответствующие вашему серверу базы данных-источника и исходной группе ресурсов.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Дополнительные сведения см. в статье [Копирование базы данных SQL Azure](sql-database-copy.md). Пример сценария см. в разделе [Пример сценария PowerShell](sql-database-copy-powershell.md#example-powershell-script).

## <a name="how-do-i-delete-a-sql-database"></a>Как удалить базу данных SQL?
Чтобы удалить базу данных SQL, используйте командлет [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx). В подписке уже должны существовать группа ресурсов, сервер и база данных.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>Удаление сервера базы данных SQL
Чтобы удалить сервер базы данных SQL, используйте командлет [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>Создание пулов эластичных баз данных и управление ими с помощью PowerShell
Дополнительные сведения о создании пулов эластичных баз данных с помощью PowerShell см. в статье [Создание эластичного пула с помощью PowerShell](sql-database-elastic-pool-manage-powershell.md).

Дополнительные сведения об управлении пулами эластичных баз данных с помощью PowerShell см. в статье [Мониторинг пула эластичных баз данных и управление им с помощью PowerShell](sql-database-elastic-pool-manage-powershell.md).

## <a name="related-information"></a>Связанные сведения
* [Командлеты Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Справка по командлетам Azure](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Feb17_HO3-->


