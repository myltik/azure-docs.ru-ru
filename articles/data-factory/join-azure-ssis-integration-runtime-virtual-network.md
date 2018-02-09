---
title: "Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети | Документация Майкрософт"
description: "Узнайте, как присоединить среду выполнения интеграции Azure SSIS к виртуальной сети Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 2131aa75dcfb975f11cff9800087c3e4e7170378
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети
Присоедините среду выполнения интеграции (IR) Azure SSIS к виртуальной сети в одной из следующих ситуаций: 

- Вы размещаете базу данных каталога SSIS в управляемом экземпляре SQL Server (закрытая предварительная версия), который является частью виртуальной сети.
- Вы хотите подключиться к локальным хранилищам данных из пакетов SSIS, работающих в среде выполнения интеграции Azure SSIS.

 Фабрика данных Azure версии 2 (предварительная версия) позволяет присоединить среду выполнения интеграции Azure SSIS к классической виртуальной сети или виртуальной сети Azure Resource Manager. 

 > [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, используйте [документацию по версии 1 фабрики данных](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Доступ к локальным хранилищам данных
Если пакеты служб SSIS используют доступ только к общедоступным облачным хранилищам данных, то нет необходимости присоединять Azure SSIS IR к виртуальной сети. Если пакеты SSIS используют доступ к локальным хранилищам данных, то необходимо присоединить Azure SSIS IR к виртуальной сети, подключенной к локальной сети. Если каталог SSIS размещен в Базе данных SQL Azure, которая не находится в виртуальной сети, то необходимо открыть соответствующие порты. Если каталог SSIS размещен в управляемом экземпляре SQL Azure, который находится в классической виртуальной сети или виртуальной сети Azure Resource Manager, то Azure SSIS IR можно присоединить к той же виртуальной сети или к другой виртуальной сети, имеющей подключение типа "виртуальная сеть — виртуальная сеть" с той сетью, в которой находится управляемый экземпляр SQL Azure. В следующих разделах приведены дополнительные сведения.

Необходимо учитывать следующие важные замечания. 

- Если есть имеющаяся виртуальная сеть, подключенная к локальной сети, то сначала создайте [виртуальную сеть Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) или [классическую виртуальную сеть](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) для присоединения своей среды выполнения интеграции Azure SSIS. Затем настройте подключение через [VPN-шлюз](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) типа "сеть — сеть" между этой виртуальной сетью и вашей локальной сетью.
- Если есть существующая виртуальная сеть Azure Resource Manager или классическая виртуальная сеть, подключенная к локальной сети в том же расположении, что и ваша среда выполнения интеграции Azure SSIS, вы можете присоединить IR к этой виртуальной сети.
- Если есть существующая классическая виртуальная сеть, подключенная к локальной сети в расположении, отличном от вашей среды выполнения интеграции Azure SSIS, вы можете сначала создать [классическую виртуальную сеть](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) для присоединения IR Azure SSIS. Затем настройте подключение типа [классическая виртуальная сеть — классическая виртуальная сеть](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). Или вы можете создать [виртуальную сеть Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network), чтобы присоединить к ней свою среду выполнения интеграции Azure SSIS. Затем настройте подключение типа [классическая виртуальная сеть — виртуальная сеть Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
- Если существует виртуальная сеть Azure Resource Manager, подключенная к локальной сети в расположении, отличном от вашей среды выполнения интеграции Azure SSIS, вы можете сначала создать [виртуальную сеть Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) для присоединения IR Azure SSIS. Затем настройте подключение типа "виртуальная сеть Azure Resource Manager — виртуальная сеть Azure Resource Manager". Кроме того, вы можете создать [классическую виртуальную сеть](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) для присоединения IR Azure SSIS. Затем настройте подключение типа [классическая виртуальная сеть — виртуальная сеть Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Сервер служб доменных имен 
Если необходимо использовать собственный сервер служб доменных имен (DNS) в виртуальной сети, присоединенной средой выполнения интеграции Azure SSIS, то следуйте инструкциям этого руководства и [убедитесь, что узлы вашей среды выполнения интеграции Azure SSIS в виртуальной сети могут разрешать конечные точки Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Группа безопасности сети
Если необходимо реализовать группу безопасности сети (NSG) в виртуальной сети, присоединенной средой выполнения интеграции Azure SSIS, то разрешите входящий и исходящий трафик через следующие порты:

| порты; | Направление | Транспортный протокол | Назначение | Входящий источник/исходящее назначение |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (при присоединении среды выполнения интеграции к классической виртуальной сети)<br/><br/>29876, 29877 (при присоединении среды выполнения интеграции к виртуальной сети Azure Resource Manager) | Входящий трафик | TCP | Службы Azure используют эти порты для взаимодействия с узлами вашей среды выполнения интеграции Azure SSIS в виртуальной сети. | Интернет | 
| 443 | Исходящие | TCP | Узлы среды выполнения интеграции Azure SSIS в виртуальной сети используют этот порт для доступа к службам Azure, например, к службе хранилища Azure, концентратору событий и т. д. | ИНТЕРНЕТ | 
| 1433<br/>11000–11999;<br/>14000–14999.  | Исходящие | TCP | Узлы среды выполнения интеграции Azure SSIS в виртуальной сети используют эти порты для доступа к базе данных SSISDB, размещенной на сервере Базы данных SQL Azure (это не относится к базе данных SSISDB, размещенной на управляемом экземпляре SQL Azure). | Интернет | 

## <a name="azure-portal-data-factory-ui"></a>Портал Azure (пользовательский интерфейс фабрики данных)
В этом разделе показано, как присоединить существующую среду выполнения Azure SSIS к виртуальной сети (классической виртуальной сети или виртуальной сети Azure Resource Manager) с помощью портала Azure и пользовательского интерфейса фабрики данных. Во-первых, необходимо правильно настроить виртуальную сеть перед присоединением среды выполнения интеграции Azure SSIS к виртуальной сети. Выполните действия, приведенные в одном из следующих двух разделов, в зависимости от типа виртуальной сети (классическая или Azure Resource Manager). Затем перейдите к третьему разделу, чтобы присоединить среду выполнения интеграции Azure SSIS к виртуальной сети. 

### <a name="use-portal-to-configure-a-classic-vnet"></a>Настройка классической виртуальной сети с помощью портала
Перед присоединением среды выполнения интеграции Azure SSIS к виртуальной сети необходимо настроить виртуальную сеть.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Больше служб**. Примените фильтр и выберите **Виртуальные сети (классика)**.
3. Примените фильтр и выберите в списке свою **виртуальную сеть**. 
4. На странице "Виртуальные сети (классика)" выберите **Свойства**. 

    ![классическая виртуальная сеть, идентификатор ресурса](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Нажмите кнопку копирования напротив поля **Идентификатор ресурса**, чтобы скопировать идентификатор ресурса для классической сети в буфер обмена. Сохраните идентификатор из буфера обмена в OneNote или в файле.
6. В меню слева выберите **Подсети** и убедитесь, что число **доступных адресов** больше, чем узлов в вашей среде выполнения интеграции Azure SSIS.

    ![Число доступных адресов в виртуальной сети](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Присоедините **MicrosoftAzureBatch** к роли **Участник классической виртуальной машины** для виртуальной сети. 
    1. В меню слева выберите "Управление доступом (IAM)" и нажмите кнопку **Добавить** на панели инструментов.
    
        ![Управление доступом -> Добавить](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. На странице **Добавление разрешений** в поле **Роль** выберите **Участник классической виртуальной машины**. Скопируйте или вставьте **ddbf3205-c6bd-46ae-8127-60eb93363864** в текстовое поле **Select** (Выбор), а затем из списка результатов поиска выберите **пакетную службу Microsoft Azure**. 
    
        ![Добавление разрешений — поиск](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Нажмите кнопку "Сохранить", чтобы сохранить параметры и закрыть страницу.
    
        ![Сохранение параметров доступа](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Убедитесь, что в списке участников отображается **MicrosoftAzureBatch**.
    
        ![Подтверждение доступа к AzureBatch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Убедитесь, что поставщик пакетной службы Azure зарегистрирован в подписке Azure, которая содержит виртуальную сеть, или зарегистрируйте этого поставщика. Если у вас в подписке уже есть учетная запись пакетной службы Azure, то ваша подписка зарегистрирована в пакетной службе Azure.
    1. На портале Azure в меню слева щелкните **Подписки**. 
    2. Выберите свою **подписку**. 
    3. Выберите **Поставщики ресурсов** в меню слева и убедитесь, что `Microsoft.Batch` является зарегистрированным поставщиком. 
    
        ![подтверждение регистрации в пакетной службе](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Если поставщик `Microsoft.Batch` не отображается в списке, то для его регистрации [создайте в своей подписке пустую учетную запись пакетной службы Azure](../batch/batch-account-create-portal.md). Затем ее можно будет удалить. 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>Настройка виртуальной сети Azure Resource Manager с помощью портала
Перед присоединением среды выполнения интеграции Azure SSIS к виртуальной сети необходимо настроить виртуальную сеть.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Больше служб**. Примените фильтр и выберите **Виртуальные сети**.
3. Примените фильтр и выберите в списке свою **виртуальную сеть**. 
4. На странице "Виртуальная сеть" выберите **Свойства**. 
5. Нажмите кнопку копирования напротив поля **RESOURCE ID** (Идентификатор ресурса), чтобы скопировать идентификатор ресурса для виртуальной сети в буфер обмена. Сохраните идентификатор из буфера обмена в OneNote или в файле.
6. В меню слева выберите **Подсети** и убедитесь, что число **доступных адресов** больше, чем узлов в вашей среде выполнения интеграции Azure SSIS.
5. Убедитесь, что поставщик пакетной службы Azure зарегистрирован в подписке Azure, которая содержит виртуальную сеть, или зарегистрируйте этого поставщика. Если у вас в подписке уже есть учетная запись пакетной службы Azure, то ваша подписка зарегистрирована в пакетной службе Azure.
    1. На портале Azure в меню слева щелкните **Подписки**. 
    2. Выберите свою **подписку**. 
    3. Выберите **Поставщики ресурсов** в меню слева и убедитесь, что `Microsoft.Batch` является зарегистрированным поставщиком. 
    
        ![подтверждение регистрации в пакетной службе](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Если поставщик `Microsoft.Batch` не отображается в списке, то для его регистрации [создайте в своей подписке пустую учетную запись пакетной службы Azure](../batch/batch-account-create-portal.md). Затем ее можно будет удалить.

### <a name="join-the-azure-ssis-ir-to-a-vnet"></a>Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети


1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Фабрики данных**. Если пункт **Фабрики данных** не отображается в меню, щелкните **Больше служб**, а затем выберите **Фабрики данных** в разделе **Аналитика**. 
    
    ![Список фабрик данных](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Выберите из списка фабрику данных со средой выполнения интеграции Azure SSIS. После этого откроется домашняя страница фабрики данных. Выберите плитку **Создать и развернуть**. На отдельной вкладке откроется пользовательский интерфейс фабрики данных. 

    ![Домашняя страница фабрики данных](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. В пользовательском интерфейсе фабрики данных перейдите на вкладку **Правка**, щелкните **Подключения**, а затем выберите вкладку **сред выполнения интеграции**. 

    ![Вкладка сред выполнения интеграции](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Если среда выполнения интеграции Azure SSIS запущена, в списке сред выполнения интеграции нажмите кнопку **Остановить** в столбце **Действия** для нужной среды выполнения интеграции Azure SSIS. Среду выполнения интеграции невозможно изменить, не остановив ее. 

    ![Остановка среды выполнения интеграции](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. В списке сред выполнения интеграции нажмите кнопку **Изменить** в столбце **Действия** для нужной среды выполнения интеграции Azure SSIS.

    ![Изменение среды выполнения интеграции](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. На странице **Общие параметры** окна **настройки среды выполнения интеграции** нажмите кнопку **Далее**. 

    ![Настройка среды выполнения интеграции — общие параметры](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. На странице **параметров SQL** введите **пароль** администратора и нажмите кнопку **Далее**.

    ![Настройка среды выполнения интеграции — параметры SQL](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. На странице **Дополнительные параметры** выполните следующие действия. 

    1. Установите флажок **Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings** (Выбрать виртуальную сеть для присоединения среды выполнения интеграции Azure SSIS и разрешить службам Azure настраивать разрешения и параметры виртуальной сети). 
    2. В поле **Тип** выберите классическую виртуальную сеть или виртуальную сеть Azure Resource Manager. 
    3. В поле **Имя виртуальной сети** выберите нужную виртуальную сеть.
    4. В поле **Имя подсети** выберите нужную подсеть в виртуальной сети. 
    5. Нажмите кнопку **Обновить**. 

        ![Настройка среды выполнения интеграции — дополнительные параметры](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Теперь можно запустить среду выполнения интеграции, нажав кнопку **запуска** в столбце **Действия** для выбранной среды выполнения интеграции Azure SSIS. Запуск IR Azure SSIS занимает приблизительно 20 минут. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-vnet"></a>Настройка виртуальной сети
Перед присоединением среды выполнения интеграции Azure SSIS к виртуальной сети необходимо настроить виртуальную сеть. Добавьте следующий сценарий, чтобы автоматически настроить разрешения или параметры виртуальной сети вашей среды выполнения интеграции Azure SSIS для присоединения виртуальной сети.

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Создание среды выполнения интеграции Azure SSIS и ее присоединение к виртуальной сети
Вы можете создать среду выполнения интеграции Azure SSIS и присоединить ее к виртуальной сети одновременно. Полный сценарий и инструкции по созданию среды выполнения интеграции Azure SSIS и ее присоединению к виртуальной сети одновременно см. в [этой статье](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Присоединение имеющейся среды выполнения интеграции Azure SSIS к виртуальной сети
В сценарии статьи о [создании среды выполнения интеграции Azure SSIS](create-azure-ssis-integration-runtime.md) показано, как создать среду выполнения интеграции Azure SSIS и присоединить ее к виртуальной сети в том же сценарии. Если у вас имеется Azure SSIS, выполните следующие действия, чтобы присоединить ее к виртуальной сети. 

1. Остановите среду выполнения интеграции Azure SSIS.
2. Настройте среду выполнения интеграции Azure SSIS для присоединения виртуальной сети. 
3. Запустите среду выполнения интеграции Azure SSIS. 

### <a name="define-the-variables"></a>Определение переменных

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet).
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Остановка среды выполнения интеграции Azure SSIS
Остановите среду выполнения интеграции Azure SSIS перед присоединением к виртуальной сети. Эта команда освобождает все узлы и прекращает выставление счетов.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Настройка параметров виртуальной сети для присоединения среды выполнения интеграции Azure SSIS
Зарегистрируйте поставщик ресурсов пакетной службы Azure:

```powershell
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

### <a name="configure-the-azure-ssis-ir"></a>Настройка среды выполнения интеграции Azure SSIS
Выполните команду Set-AzureRmDataFactoryV2IntegrationRuntime, чтобы настроить среду выполнения интеграции Azure SSIS для присоединения виртуальной сети: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Запуск среды выполнения интеграции Azure SSIS
Выполните следующую команду для запуска среды выполнения интеграции SSIS Azure: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Выполнение этой команды занимает от **20 до 30 минут**.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о среде выполнения Azure SSIS см. в следующих разделах: 

- [Среда выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). В этой статье содержатся общие сведения о средах выполнения интеграции в целом, включая Azure SSIS IR. 
- [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-create-azure-ssis-runtime-portal.md). Эта статья содержит пошаговые инструкции для создания Azure SSIS IR и использует базу данных SQL Azure для размещения каталога SSIS. 
- [Создание среды выполнения интеграции Azure SSIS](create-azure-ssis-integration-runtime.md). Эта статья дополняет соответствующее руководство, а также предоставляет инструкции по использованию управляемого экземпляра SQL Azure (закрытая предварительная версия) и присоединению среды выполнения интеграции к виртуальной сети. 
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этом статье показано, как извлечь сведения о среде выполнения интеграции Azure SSIS и описания состояний из возвращаемых данных. 
- [Управление средой выполнения интеграции Azure SSIS](manage-azure-ssis-integration-runtime.md). В этой статье показано, как остановить, запустить или удалить Azure SSIS IR. В ней также показано, как развернуть Azure SSIS IR путем добавления дополнительных узлов в среду выполнения интеграции. 
