---
title: "Powershell: начало работы с базой данных SQL Azure | Документация Майкрософт"
description: "Узнайте, как создать логический сервер базы данных SQL, правила брандмауэра на уровне сервера и базы данных с помощью PowerShell. Вы также научитесь отправлять запросы к базам данных с помощью PowerShell."
keywords: "создание новой базы данных SQL, настройка базы данных"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 12/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 7f1983e871ea425a095e3a4bf6c7a1b89d6f45a9
ms.openlocfilehash: 51ed691eb206f284cb8bbd682c723f87362caa33


---

# <a name="tutorial-provision-and-access-an-azure-sql-database-using-powershell"></a>Руководство по подготовке базы данных SQL Azure и доступу к ней с помощью портала PowerShell

В этом руководстве по началу работы вы узнаете, как с помощью PowerShell выполнить следующее:

* создадим группу ресурсов Azure;
* создадим логический сервер SQL Azure;
* просмотреть свойства сервера Azure SQL Server;
* создадим правило брандмауэра на уровне сервера;
* создать пример отдельной базы данных AdventureWorksLT;
* просмотреть свойства образца базы данных AdventureWorksLT;
* создать пустую отдельную базу данных.

В этом руководстве вы также узнаете, как:

* подключения к логическому серверу и его базе данных master;
* просмотра свойств базы данных master;
* подключения к примеру базы данных;
* просмотра свойств пользовательской базы данных.

После завершения работы с этим руководством у вас будет пример базы данных и пустая база данных, подключенные к логическому серверу и работающие в группе ресурсов Azure. У вас также будет правило брандмауэра на уровне сервера, которое будет разрешать субъектам уровня сервера входить на сервер из указанного IP-адреса (или диапазона IP-адресов). 

**Оценка времени.** Для работы с этим руководством потребуется около 30 минут (при условии, что предварительные требования уже выполнены).


> [!TIP]
> Во время работы с руководством по началу работы эти же задачи можно выполнять с помощью [портала Azure](sql-database-get-started.md).
>


## <a name="prerequisites"></a>Предварительные требования

* Вам понадобится учетная запись Azure. Вы можете [создать бесплатную учетную запись Azure](/pricing/free-trial/?WT.mc_id=A261C142F) или [активировать преимущества для подписчиков Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Необходимо войти в систему, используя учетную запись, которой назначена роль владельца или участника подписки. Дополнительные сведения об управлении доступом на основе ролей (RBAC) см. в статье [Начало работы с управлением доступом на портале Azure](../active-directory/role-based-access-control-what-is.md).

* BACPAC-файл образца базы данных AdventureWorksLT должен находиться в хранилище BLOB-объектов Azure.

### <a name="download-the-adventureworkslt-sample-database-bacpac-file-and-save-it-in-azure-blob-storage"></a>Скачивание BACPAC-файла образца базы данных AdventureWorksLT и сохранение его в хранилище BLOB-объектов Azure

В этом руководстве рассматривается создание базы данных AdventureWorksLT путем импорта BACPAC-файла из службы хранилища Azure. В первую очередь нужно получить копию файла AdventureWorksLT.bacpac и отправить ее в хранилище BLOB-объектов.
Чтобы подготовить образец базы данных к импорту, сделайте следующее:

1. [Скачайте файл AdventureWorksLT.bacpac](https://sqldbbacpacs.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac) и сохраните его с расширением BACPAC.
2. [Создайте учетную запись хранения.](../storage/storage-create-storage-account.md#create-a-storage-account) Вы можете создать учетную запись хранения с параметрами по умолчанию.
3. Создайте **контейнер**. Для этого перейдите к учетной записи хранения, выберите **Большие двоичные объекты**, а затем щелкните **+Container** (Добавить контейнер).
4. Отправьте BACPAC-файл в контейнер больших двоичных объектов в своей учетной записи хранения. Это можно сделать с помощью кнопки **Отправить**, расположенной в верхней части страницы контейнера, или средства [AzCopy](../storage/storage-use-azcopy.md#blob-upload). 
5. После сохранения файла AdventureWorksLT.bacpac вам нужно получить URL-адрес и ключ учетной записи хранения, которые потребуются в фрагменте кода для импорта, приведенном позже в этом руководстве. 
   * Выберите BACPAC-файл и скопируйте URL-адрес. Этот URL-адрес имеет следующий формат: https://{имя_учетной_записи_хранения}.blob.core.windows.net/{имя_контейнера}/AdventureWorksLT.bacpac. На странице учетной записи хранения щелкните **Ключи доступа** и скопируйте значение **key1**.


[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Создание логического сервера SQL Server с помощью Azure PowerShell

В рамках этого руководства вам потребуется группа ресурсов, где будет содержатся сервер, поэтому в первую очередь создадим группу ресурсов и сервер ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup), [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)) или получим ссылки на имеющиеся ресурсы ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup), [Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver)).
Следующие фрагменты кода позволяют создать группу ресурсов и сервер Azure SQL Server или получить имеющиеся.

Список допустимых расположений Azure и формат строки см. в разделе [Вспомогательные фрагменты кода](#helper-snippets) ниже.
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>Просмотр свойств логического сервера SQL Server с помощью Azure PowerShell

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Создание правила брандмауэра серверного уровня с помощью Azure PowerShell

Чтобы задать правило брандмауэра, необходимо знать свой общедоступный IP-адрес. Его можно получить в любом браузере (введите поисковый запрос "мой IP-адрес"). Дополнительные сведения см. в статье [Обзор правил брандмауэра базы данных SQL Azure](sql-database-firewall-configure.md).

В следующем фрагменте кода используются командлеты [Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) и [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule). Они позволяют создать правило или получить ссылку на имеющееся. Если правило уже имеется, этот фрагмент кода только получает ссылку на него, но не обновляет начальные и конечные IP-адреса. В предложении **else** всегда можно добавить командлет [Set-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule). Он позволяет создать или изменить функции.

> [!NOTE] 
> Брандмауэр базы данных SQL на сервере можно открыть для одного IP-адреса или целого диапазона IP-адресов. Открыв брандмауэр, вы дадите возможность администраторам и пользователям SQL входить в любую базу данных на сервере, для которой у них есть действительные учетные данные.
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule
```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Подключение к SQL Server с помощью Azure PowerShell

Давайте выполним быстрый запрос к базе данных master, чтобы проверить, есть ли подключение к серверу. В следующем фрагменте кода используется [поставщик платформы .NET Framework для SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Он позволяет подключиться и отправить запрос к базе данных master сервера. Этот фрагмент кода создает строку подключения на основе переменных, используемых в предыдущих фрагментах. Замените значения заполнителей на учетную запись и пароль администратора SQL Server, используемые для создания сервера на предыдущих этапах.


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Создание образца базы данных AdventureWorksLT с помощью Azure PowerShell

Командлет [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport) в следующем фрагменте кода позволяет импортировать BACPAC-файл образца базы данных AdventureWorksLT. BACPAC-файл расположен в хранилище BLOB-объектов Azure. После запуска командлета импорта ход выполнения этой операции можно отслеживать с помощью командлета [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus).
$storageUri — это свойство URL-адреса BACPAC-файла, отправленного ранее на портал. Оно должно иметь следующий формат: https://{учетная_запись_хранения}.blob.core.windows.net/{контейнер}/AdventureWorksLT.bacpac.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "StorageAccessKey"
$storageUri = "{storage-uri}" # URL of bacpac file you uploaded to your storage account
$storageKey = "{storage-key}" # key1 in the Access keys setting of your storage account

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Просмотр свойств базы данных с помощью Azure PowerShell

После создания базы данных вы можете просмотреть некоторые ее свойства.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Подключение и выполнение запроса к образцу базы данных с помощью Azure PowerShell

Давайте выполним быстрый запрос к базе данных AdventureWorksLT, чтобы проверить, можем ли мы к ней подключиться. В следующем фрагменте кода используется [поставщик платформы .NET Framework для SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Он позволяет подключиться и отправить запрос к базе данных. Этот фрагмент кода создает строку подключения на основе переменных, используемых в предыдущих фрагментах. Замените значение заполнителя на пароль администратора SQL Server.

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Создание пустой базы данных с помощью Azure PowerShell

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>Полный сценарий Azure PowerShell для создания сервера, правила брандмауэра и базы данных



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"

$myStorageKeyType = "StorageAccessKey"
# Get these values from your Azure storage account:
$myStorageUri = "{http://your-storage-account.blob.core.windows.net/your-container/AdventureWorksLT.bacpac}"
$myStorageKey = "{your-storage-key}"


# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin


# Create or update server firewall rule
#######################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> Чтобы сэкономить деньги во время обучения, удалите неиспользуемые базы данных. Базы данных выпуска "Базовый" можно восстановить в течение 7 дней. Ни в коем случае не удаляйте сервер. Сделав это, вы не сможете восстановить ни сам север, ни его базы данных.

## <a name="helper-snippets"></a>Вспомогательные фрагменты кода

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> Чтобы сэкономить деньги во время обучения, удалите неиспользуемые базы данных. Базы данных выпуска "Базовый" можно восстановить в течение семи дней. Ни в коем случае не удаляйте сервер. Сделав это, вы не сможете восстановить ни сам север, ни его базы данных.
>

## <a name="next-steps"></a>Дальнейшие действия
Завершив работу с этим первым руководством по началу работы и создав базу данных с демонстрационными данными, изучите дополнительные материалы, в которых рассматривается начатая в этом руководстве тема. 

- Основные сведения об аутентификации SQL Server см. в статье [Руководство по базам данных SQL: аутентификация, доступ и правила брандмауэра уровня базы данных в SQL Server](sql-database-control-access-sql-authentication-get-started.md).
- Основные сведения об аутентификации Azure Active Directory см. в статье [Руководство по базам данных SQL: доступ с аутентификацией Azure AD и правила брандмауэра уровня базы данных](sql-database-control-access-aad-authentication-get-started.md).
* Если вы хотите поработать с запросами к примеру базы данных на портале Azure, см. страницу [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/) (Предварительная версия: интерактивные запросы к базам данных SQL).
* Если вы знаете Excel, узнайте, как [подключиться к базе данных SQL в Azure с помощью Excel](sql-database-connect-excel.md).
* Если вы готовы написать свой собственный код, выберите язык программирования в [библиотеках подключений для базы данных SQL и SQL Server](sql-database-libraries.md).
* Если вы хотите перенести локальные базы данных SQL Server в облако Azure, см. статью [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).
* Чтобы загрузить данные в новую таблицу из CSV-файла с помощью программы командной строки BCP, см. сведения в статье [Загрузка данных из CSV-файла в хранилище данных SQL Azure (неструктурированные файлы)](sql-database-load-from-csv-with-bcp.md).
* Сведения о том, как создавать таблицы и другие объекты, см. в разделе "Создание таблицы" в статье [Создание таблицы (Учебник)](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Дополнительные ресурсы
[Что такое база данных SQL?](sql-database-technical-overview.md)



<!--HONumber=Feb17_HO2-->


