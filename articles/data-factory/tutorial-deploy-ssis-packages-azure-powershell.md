---
title: Развертывание пакетов служб SSIS в Azure с помощью PowerShell | Документация Майкрософт
description: В этой статье объясняется, как развертывать пакеты служб SSIS в Azure и создать среду выполнения интеграции Azure-SSIS с использованием PowerShell.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: aab864696be7121be049ce4e907b10431a7b63cb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure-with-powershell"></a>Развертывание пакетов SQL Server Integration Services в Azure с помощью PowerShell
В этом руководстве представлены шаги по подготовке среды выполнения интеграции (IR) Azure SSIS в фабрике данных. Затем можно использовать SQL Server Data Tools (SSDT) ​​или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS для этой среды выполнения в Azure. Вот какие шаги выполняются в этом руководстве:

> [!NOTE]
> В этой статье используется Azure PowerShell для подготовки служб среды выполнения интеграции Integration Services Azure. Дополнительные сведения о работе с пользовательским интерфейсом фабрики данных для подготовки среды выполнения интеграции Integration Services Azure см. в руководстве по [созданию среды выполнения интеграции SSIS Azure](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создание среды выполнения интеграции Azure SSIS.
> * Запуск среды выполнения интеграции Azure SSIS.
> * Развертывание пакетов служб SSIS.
> * Проверка всего скрипта.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).



## <a name="prerequisites"></a>предварительным требованиям
- **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу. См. дополнительные сведения о [среде выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Сервер базы данных SQL Azure**. Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. На этом сервере размещается база данных каталога служб SSIS (SSISDB). Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в SSISDB, не пересекая регионы Azure. 
    - Убедитесь, что для сервера базы данных **включена** настройка **Разрешить доступ к службам Azure**. Дополнительные сведения см. в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Сведения о включении этого параметра с помощью PowerShell см. в статье о [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Добавьте IP-адрес клиентского компьютера или диапазон IP-адресов, который включает IP-адрес клиентского компьютера, в список IP-адресов клиента в параметрах брандмауэра для сервера базы данных. Дополнительные сведения см. в разделе [Правила брандмауэра уровня сервера и уровня базы данных SQL Azure](../sql-database/sql-database-firewall-configure.md). 
    - Убедитесь, что на сервере базы данных SQL Azure нет каталога SSIS (базы данных SSIDB). При подготовке среды Azure SSIS IR не поддерживается использование существующего каталога SSIS. 
- **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps). Вы используете PowerShell для запуска скрипта, чтобы подготовить среду выполнения интеграции Azure SSIS, запускающую пакеты SSIS в облаке. 

> [!NOTE]
> - Вы можете создать фабрику данных версии 2 в следующих регионах: восточная часть США, восточная часть США 2, Юго-Восточная Азия и Западная Европа. 
> - Среду IR Azure-SSIS можно создать в следующих регионах: восточная часть США, восточная часть США 2, центральная часть США, Северная Европа, Западная Европа и Восточная Австралия.

## <a name="launch-windows-powershell-ise"></a>Запуск интегрированной среды сценариев Windows PowerShell
Откройте **интегрированную среду сценариев Windows PowerShell** с правами администратора. 

## <a name="create-variables"></a>Создание переменных
Скопируйте и вставьте следующий скрипт. Укажите значения для переменных. Список поддерживаемых **ценовых категорий** базы данных SQL Azure см. в статье [Ограничения ресурсов базы данных SQL Azure](../sql-database/sql-database-resource-limits.md).

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"

# You can create Azure-SSIS IR in the following regions: East US, East US 2, Central US, North Europe, West Europe, Australia East 
$AzureSSISLocation = "EastUS"
 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>Проверьте подключение к базе данных
Добавьте следующий скрипт для проверки сервера базы данных SQL Azure server.database.windows.net. 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

Чтобы создать базу данных Azure SQL в рамках скрипта, см. следующий пример: 

Задайте значения для переменных, которые еще не были определены. Например: FirewallIPAddress. 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
  -ServerName $SQLServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SQLServerAdmin, $(ConvertTo-SecureString -String $SQLServerPass -AsPlainText -Force))

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SQLServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SQLServerName -AllowAllAzureIPs
```


## <a name="log-in-and-select-subscription"></a>Вход и выбор подписки
Добавьте следующий код в скрипт, чтобы войти и выбрать подписку Azure: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте [группу ресурсов Azure ](../azure-resource-manager/resource-group-overview.md) с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

Если группа ресурсов уже существует, не копируйте этот код в скрипт. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Создание фабрики данных
Чтобы создать фабрику данных, выполните следующие команды:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Создание среды выполнения интеграции
Выполните следующую команду для создания среды выполнения интеграции Azure SSIS, запускающей пакеты служб SSIS в Azure: 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode 
```

## <a name="start-integration-runtime"></a>Запуск среды выполнения интеграции
Выполните следующую команду для запуска среды выполнения интеграции SSIS Azure: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Выполнение этой команды занимает от **20 до 30 минут**. 

## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.
Теперь используйте SQL Server Data Tools (SSDT) или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS в Azure. Подключитесь к серверу Azure SQL, на котором размещен каталог служб SSIS (SSISDB). Имя сервера Azure SQL Server имеет формат `<servername>.database.windows.net` (для базы данных SQL Azure). 

См. документацию SSIS в следующих статьях: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Развертывание, запуск и отслеживание пакета SSIS в Azure)   
- [Connect to the SSISDB Catalog database on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Подключение к базе данных каталога SSISDB в Azure)
- [Schedule the execution of an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Планирование выполнения пакета SSIS в Azure)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Подключение к локальным источникам данных с помощью аутентификации Windows) 

## <a name="full-script"></a>Полный сценарий
В этой версии вы должны использовать PowerShell для подготовки экземпляра среды выполнения интеграции Azure SSIS, запускающей пакеты SSIS в облаке. Сейчас невозможно подготовить эту среду выполнения с помощью портала Azure. 

Скрипт PowerShell в этом разделе настраивает экземпляр среды выполнения интеграции Azure SSIS, которая запускает пакеты SSIS, в облаке. После успешного выполнения этого скрипта можно развернуть и выполнить пакеты служб SSIS в облаке Microsoft Azure с базой данных SSISDB, размещенной в базе данных SQL Azure.

1. Откройте интегрированную среду сценариев (ISE) Windows PowerShell.
2. В ISE введите в окне командной строки следующую команду:    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Скопируйте скрипт PowerShell в этом разделе и вставьте его в ISE.
4. Укажите соответствующие значения параметров скрипта в разделе SSIS in Azure specifications (Спецификации SSIS в Azure) в начале скрипта. Эти параметры описаны в следующем разделе.
5. Выполните скрипт. Команда `Start-AzureRmDataFactoryV2IntegrationRuntime` ближе к концу скрипта выполняется около **20–30 минут**.

> [!NOTE]
> - Скрипт подключается к базе данных SQL Azure для подготовки базы данных каталога SSIS (SSISDB). Скрипт также настраивает разрешения и параметры виртуальной сети, если это указано, и подключает к ней новый экземпляр среды выполнения интеграции Azure SSIS.
> - При подготовке экземпляра среды выполнения интеграции Azure Integration Services также устанавливается пакет функций Azure для служб SSIS и распространяемый компонент Access. Эти компоненты обеспечивают подключение к файлам Excel и Access и другим источникам данных Azure, кроме тех, которые поддерживаются встроенными компонентами. Сейчас вы не можете установить сторонние компоненты для служб SSIS (в том числе такие сторонние компоненты от Майкрософт, как компоненты Oracle и Teradata от Attunity и SAP BI).


Список поддерживаемых **ценовых категорий** базы данных SQL Azure см. в статье [Ограничения ресурсов базы данных SQL Azure](../sql-database/sql-database-resource-limits.md). 

Список регионов, поддерживаемых фабрикой данных Azure версии 2 и инфраструктурой Integration Runtime служб Azure SSIS, приведен на странице [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/). Разверните категорию **Данные и аналитика**, чтобы отобразились **фабрика данных версии 2** и **инфраструктура Integration Runtime служб SSIS**.

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети
Если вы используете управляемый экземпляр SQL Azure (предварительная версия) в качестве каталога SQL Server Integration Services (SSIS) внутри виртуальной сети, вам нужно присоединить среду выполнения интеграции Azure SSIS к той же виртуальной сети. Фабрика данных Azure версии 2 (предварительная версия) позволяет присоединить среду выполнения интеграции Azure SSIS к виртуальной сети. Дополнительные сведения см. в статье [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md).

Полный скрипт для создания среды выполнения Azure SSIS, которая присоединяется к виртуальной сети, см. в статье [Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Мониторинг среды выполнения интеграции IR Azure SSIS и управление ею
Сведения о мониторинге и администрировании среды выполнения интеграции см. по ссылкам ниже. 

- [Среда выполнения интеграции Azure SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Управление средой выполнения интеграции Azure SSIS](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи: 

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создание среды выполнения интеграции Azure SSIS.
> * Запуск среды выполнения интеграции Azure SSIS.
> * Развертывание пакетов служб SSIS.
> * Проверка всего скрипта.

Перейдите к следующему руководству, чтобы узнать о копировании данных из локальной среды в облако: 

> [!div class="nextstepaction"]
>[Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью фабрики данных Azure](tutorial-copy-data-dot-net.md)
