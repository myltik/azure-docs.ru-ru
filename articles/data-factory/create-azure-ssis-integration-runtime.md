---
title: Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как создать среду выполнения интеграции Azure SSIS для запуска пакета служб SSIS в облаке Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e0217080963502094800a8b62de9c781817aec61
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure
В этой статье представлены шаги по подготовке среды выполнения интеграции Azure SSIS в фабрике данных Azure. Затем можно использовать SQL Server Data Tools (SSDT) ​​или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS для этой среды выполнения в Azure.

В руководстве [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-create-azure-ssis-runtime-portal.md) было показано, как создать среду выполнения интеграции (IR) Azure SSIS, используя базу данных SQL Azure в качестве хранилища для каталога служб SSIS. Эта статья дополняет руководство и содержит инструкции по: 

- использованию Управляемого экземпляра SQL Azure (предварительная версия) для размещения каталога SSIS (база данных SSISDB);
- присоединению Azure SSIS IR к виртуальной сети Azure. Общие сведения о присоединении Azure SSIS IR к виртуальной сети и настройке виртуальной сети на портале Azure см. в статье [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md). 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-introduction.md).


## <a name="overview"></a>Обзор
В этой статье показаны различные способы подготовки среды выполнения интеграции Azure SSIS IR:

- [портал Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Шаблон Azure Resource Manager](#azure-resource-manager-template)

При создании среды выполнения интеграции Azure SSIS фабрика данных подключается к базе данных SQL Azure для подготовки базы данных каталога SSIS (SSISDB). Скрипт также настраивает разрешения и параметры виртуальной сети, если это указано, и подключает к ней новый экземпляр среды выполнения интеграции Azure SSIS.

При подготовке экземпляра среды выполнения интеграции Azure Integration Services также устанавливается пакет функций Azure для служб SSIS и распространяемый компонент Access. Эти компоненты обеспечивают подключение к файлам Excel и Access и другим источникам данных Azure, кроме тех, которые поддерживаются встроенными компонентами. Кроме того, вы можете установить дополнительные компоненты. Дополнительные сведения см. в статье о [настройке среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure**. Если у вас нет подписки, вы можете [создать бесплатную пробную учетную запись](http://azure.microsoft.com/pricing/free-trial/).
- **Сервер базы данных SQL Azure** или **Управляемый экземпляр SQL Server (предварительная версия)**. Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. На этом сервере размещается база данных каталога служб SSIS (SSISDB). Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в SSISDB, не пересекая регионы Azure. Запишите ценовую категорию своего сервера Azure SQL. Список поддерживаемых ценовых категорий базы данных SQL Azure см. в статье [Ограничения ресурсов базы данных SQL Azure](../sql-database/sql-database-resource-limits.md).

    Убедитесь, что сервер базы данных SQL Azure или Управляемый экземпляр SQL Server (предварительная версия) не имеют каталога служб SSIS (база данных SSIDB). При подготовке среды Azure SSIS IR не поддерживается использование существующего каталога SSIS.
- **Классическая виртуальная сеть или виртуальная сеть Azure Resource Manager (необязательно)**. Виртуальная сеть Azure нужна, если выполняется хотя бы одно из следующих условий:
    - Вы размещаете базу данных каталога SSIS в Управляемом экземпляре SQL Server (предварительная версия), который является частью виртуальной сети.
    - Вы хотите подключиться к локальным хранилищам данных из пакетов SSIS, работающих в среде выполнения интеграции Azure SSIS.
- **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps). Вы используете PowerShell для запуска скрипта, чтобы подготовить среду выполнения интеграции Azure SSIS, запускающую пакеты SSIS в облаке. 

> [!NOTE]
> - Вы можете создать фабрику данных версии 2 в следующих регионах: восточная часть США, восточная часть США 2, Юго-Восточная Азия и Западная Европа. 
> - Среду IR Azure-SSIS можно создать в следующих регионах: восточная часть США, восточная часть США 2, центральная часть США, западная часть США 2, Северная Европа, Западная Европа, южная часть Соединенного Королевства и восточная Австралия.

## <a name="azure-portal"></a>Портал Azure
В этом разделе для создания среды выполнения интеграции Azure SSIS используется портал Azure, в частности пользовательский интерфейс фабрики данных. 

### <a name="create-a-data-factory"></a>Создание фабрики данных

1. Запустите веб-браузер **Microsoft Edge** или **Google Chrome**. Сейчас только эти браузеры поддерживают пользовательский интерфейс фабрики данных.
2. Войдите на [портал Azure](https://portal.azure.com/).    
3. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. На странице **Новая фабрика данных** введите **MyAzureSsisDataFactory** в поле **Имя**. 
      
     ![Страница "Новая фабрика данных"](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть **глобально уникальным**. Если вы получите указанную ниже ошибку, введите другое имя фабрики данных (например, ваше_имя_MyAzureSsisDataFactory) и попробуйте создать фабрику данных снова. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных".
  
       `Data factory name “MyAzureSsisDataFactory” is not available`

3. Выберите **подписку** Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий.
     
      - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
      - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
      Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
4. Выберите **V2 (Preview)** (V2 (предварительная версия)) для **версии**.
5. Укажите **расположение** фабрики данных. В списке отображаются только те расположения, в которых можно создать фабрики данных.
6. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
7. Нажмите кнопку **Создать**.
8. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже.
   
   ![Домашняя страница фабрики данных](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Щелкните **Создание и мониторинг**, чтобы открыть на отдельной вкладке пользовательский интерфейс фабрики данных. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Подготовка среды выполнения интеграции Azure SSIS

1. На странице начала работы щелкните плитку **Configure SSIS Integration Runtime** (Настройка среды выполнения интеграции SSIS). 

   ![Плитка настройки среды выполнения интеграции SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. На странице **General Settings** (Общие параметры) для **настройки среды выполнения интеграции** выполните следующие действия: 

   ![Общие параметры](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Укажите **имя** среды выполнения интеграции.
    2. Укажите **расположение** среды выполнения интеграции. Здесь отображаются только поддерживаемые расположения.
    3. Выберите **размер узла**, который нужно настроить с помощью среды выполнения интеграции SSIS.
    4. Укажите **количество узлов** в кластере.
    5. Нажмите кнопку **Далее**. 
1. В разделе **SQL Settings** (Параметры SQL) выполните следующие действия: 

    ![Раздел "SQL Settings" (Параметры SQL)](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Укажите **подписку** Azure, в которой размещен сервер Azure SQL. 
    2. Выберите нужный сервер Azure SQL в списке **Catalog Database Server Endpoint** (Конечная точка сервера базы данных каталога).
    3. Введите имя пользователя учетной записи **администратора**.
    4. Введите **пароль** администратора.  
    5. Выберите **уровень обслуживания** для базы данных SSISDB. По умолчанию используется уровень "Базовый".
    6. Нажмите кнопку **Далее**. 
1.  На странице **Advanced Settings** (Дополнительные параметры) выберите значение для параметра **Maximum Parallel Executions Per Node** (Максимальное количество параллельных выполнений на каждом узле).   

    ![Дополнительные параметры](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Этот шаг **не является обязательным**. Если у вас есть виртуальная сеть (классическая или Azure Resource Manager), к которой вы хотите присоединить среду выполнения интеграции, выберите параметр **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Выбрать виртуальную сеть для присоединения среды выполнения интеграции Azure SSIS и разрешить службам Azure настроить параметры и разрешения для виртуальной сети) и выполните следующие действия. 

    ![Расширенные параметры для виртуальной сети](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. В поле **Подписка** укажите **подписку**, в которой размещена виртуальная сеть. 
    2. В поле "Тип" укажите **тип** виртуальной сети (классическая виртуальная сеть или виртуальная сеть Azure Resource Manager). 
    3. В поле **Имя виртуальной сети** выберите имя **виртуальной сети**. 
    4. В поле **Имя подсети** выберите имя **подсети** в виртуальной сети.
1. Щелкните **Finish** (Готово), чтобы начать создание среды выполнения интеграции Azure SSIS. 

    > [!IMPORTANT]
    > - Эта процедура занимает около 20 минут.
    > - Служба фабрики данных подключается к базе данных SQL Azure для подготовки базы данных каталога SSIS. Скрипт также настраивает разрешения и параметры виртуальной сети, если это указано, и подключает к ней новый экземпляр среды выполнения интеграции Azure SSIS.
7. В окне **Connections** (Подключения) перейдите на **среду выполнения интеграции**. Чтобы обновить состояние, щелкните **Refresh** (Обновить). 

    ![Состояние создания](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. С помощью ссылок в столбце **Actions** (Действия) вы можете остановить, запустить, изменить или удалить среду выполнения интеграции. Последняя из этих ссылок позволяет просмотреть код JSON для среды выполнения интеграции. Кнопки изменения и удаления действуют только в том случае, если среда выполнения интеграции остановлена. 

    ![Среда выполнения интеграции Azure SSIS — действия](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Среда выполнения интеграции Azure SSIS на портале

1. В пользовательском интерфейсе фабрики данных Azure перейдите на вкладку **Изменить**, щелкните **Подключения**, а затем откройте вкладку **Integration Runtimes** (Среды выполнения интеграции), чтобы просмотреть все существующие в фабрике данных среды выполнения интеграции. 
    ![Просмотр существующих сред выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Щелкните **New** (Создать), чтобы создать новую среду выполнения интеграции Azure SSIS. 

    ![Доступ к среде выполнения интеграции через меню](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Чтобы создать среду выполнения интеграции Azure SSIS, щелкните действие **New** (Создать), как показано на рисунке. 
3. В окне настройки среды выполнения интеграции выберите **Lift-and-shift existing SSIS packages to execute in Azure** (Перенос существующих пакетов служб SSIS по методике lift-and-shift для выполнения в Azure), а затем щелкните **Next** (Далее).

    ![Указание типа среды выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Остальные шаги описаны в разделе [Подготовка среды выполнения интеграции Azure SSIS](#provision-an-azure-ssis-integration-runtime).

## <a name="azure-powershell"></a>Azure PowerShell
В этом разделе для создания среды выполнения интеграции Azure SSIS используется Azure PowerShell.

### <a name="create-variables"></a>Создание переменных
Определите переменные для использования в скрипте этого руководства:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (Preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Вход и выбор подписки
Добавьте следующий код в скрипт для выполнения входа и выбора подписки Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Проверьте подключение к базе данных
Добавьте следующий скрипт для проверки сервера базы данных Azure SQL server.database.windows.net или конечной точки сервера Управляемого экземпляра SQL Azure (предварительная версия). 

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

### <a name="configure-virtual-network"></a>Настройка виртуальной сети
Добавьте следующий скрипт, чтобы автоматически настроить разрешения или параметры виртуальной сети вашей среды интеграции Azure SSIS.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте [группу ресурсов Azure ](../azure-resource-manager/resource-group-overview.md) с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Создание фабрики данных
Чтобы создать фабрику данных, выполните следующие команды:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Создание среды выполнения интеграции
Для создания среды выполнения интеграции Azure SSIS, которая запускает пакеты службы SSIS, выполните следующую команду. Используйте сценарий из раздела на основе типа используемой базы данных (база данных SQL Azure и Управляемый экземпляр SQL Azure (предварительная версия)). 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Использование базы данных SQL Azure для размещения базы данных SSISDB (каталог SSIS) 

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
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri
```

Не нужно передавать значения для VNetId и Subnet, если не требуется доступ к данным в локальной среде, т. е. если у вас есть локальные источники или целевые расположения данных в пакетах SSIS. Необходимо передать значение для параметра CatalogPricingTier. Список поддерживаемых ценовых категорий базы данных SQL Azure см. в статье [Ограничения ресурсов базы данных SQL Azure](../sql-database/sql-database-resource-limits.md).

#### <a name="azure-sql-managed-instance-preview-to-host-the-ssisdb-database"></a>Управляемый экземпляр SQL Azure (предварительная версия) для размещения базы данных SSISDB

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Необходимо передать значения параметрам VnetId и Subnet с управляемым экземпляром SQL Azure (предварительная версия), который присоединяется к виртуальной сети. Параметр CatalogPricingTier не применяется к управляемому экземпляру SQL Azure (предварительная версия). 

### <a name="start-integration-runtime"></a>Запуск среды выполнения интеграции
Выполните следующую команду для запуска среды выполнения интеграции SSIS Azure: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Выполнение этой команды занимает от **20 до 30 минут**. 


### <a name="full-script"></a>Полный сценарий
Ниже приведен полный сценарий, который создает среду выполнения интеграции Azure SSIS и присоединяет ее к виртуальной сети. Этот сценарий предполагает, что Управляемый экземпляр SQL Azure (предварительная версия) используется для размещения каталога служб SSIS. 

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
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

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

Connect-AzureRmAccount
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
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Шаблон диспетчера ресурсов Azure
В этом разделе для создания среды выполнения интеграции Azure SSIS используется шаблон Azure Resource Manager. Ниже приведен пример пошагового руководства. 

1. Создайте файл JSON со следующим шаблоном Resource Manager. Замените значения в угловых скобках (заполнители) на собственные. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Для развертывания шаблона Resource Manager выполните команду New-AzureRmResourceGroupDeployment, как показано в следующем примере. В этом примере ADFTutorialResourceGroup — имя группы ресурсов. ADFTutorialARM.json — файл, который содержит определение JSON для фабрики данных и среды выполнения интеграции Azure SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    С помощью этой команды создается фабрика данных, а в ней — среда выполнения интеграции Azure SSIS, но текущая команда не запускает среду выполнения интеграции. 
3. Чтобы запустить среду выполнения интеграции Azure SSIS, выполните команду Start-AzureRmDataFactoryV2IntegrationRuntime. 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.
Теперь используйте SQL Server Data Tools (SSDT) или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS в Azure. Подключитесь к серверу Azure SQL, на котором размещен каталог служб SSIS (SSISDB). Имя сервера Azure SQL имеет формат &lt;servername&gt;.database.windows.net (для базы данных SQL Azure). Дополнительные сведения см. в разделе [Развертывание пакетов на сервере служб Integration Services](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

## <a name="next-steps"></a>Дополнительная информация
См. дополнительные сведения об Azure SSIS IR в этой документации:

- [Среда выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). В этой статье содержатся общие сведения о средах выполнения интеграции в целом, включая Azure SSIS IR. 
- [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-create-azure-ssis-runtime-portal.md). Эта статья содержит пошаговые инструкции для создания Azure SSIS IR и использует базу данных SQL Azure для размещения каталога SSIS. 
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этом статье показано, как извлечь сведения о среде выполнения интеграции Azure SSIS и описания состояний из возвращаемых данных. 
- [Управление средой выполнения интеграции Azure SSIS](manage-azure-ssis-integration-runtime.md). В этой статье показано, как остановить, запустить или удалить Azure SSIS IR. В ней также показано, как развернуть Azure SSIS IR путем добавления дополнительных узлов в среду выполнения интеграции. 
- [Join an Azure-SSIS integration runtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети). В этой статье содержатся общие сведения о присоединении среды выполнения интеграции SSIS Azure к виртуальной сети Azure. В ней также показано, как настроить виртуальную сеть, чтобы присоединить среду выполнения интеграции SSIS Azure к виртуальной сети с помощью портала Azure. 
