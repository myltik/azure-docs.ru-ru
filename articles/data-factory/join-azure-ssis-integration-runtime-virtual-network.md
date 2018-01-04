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
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: aa570379890023c83383d291aa5d57fb79b2d5aa
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети
Среду выполнения интеграции (IR) Azure SSIS необходимо присоединить к виртуальной сети, если выполняется хотя бы одно из следующих условий: 

- вы размещаете базу данных каталога SSIS в управляемом экземпляре SQL Server (закрытая предварительная версия), который является частью виртуальной сети;
- вы хотите подключиться к локальным хранилищам данных из пакетов SSIS, работающих в среде выполнения интеграции Azure SSIS.

 Фабрика данных Azure версии 2 (предварительная версия) позволяет присоединить среду выполнения интеграции Azure SSIS к классической виртуальной сети. В настоящее время не поддерживается виртуальной сети Azure Resource Manager. Однако есть обходной путь, который описан в следующем разделе. 

 > [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, используйте [документацию по версии 1 фабрики данных](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Доступ к локальным хранилищам данных
Если пакеты служб SSIS используют доступ только к общедоступным облачным хранилищам данных, то нет необходимости присоединять Azure SSIS IR к виртуальной сети. Если пакеты SSIS используют доступ к локальным хранилищам данных, то необходимо присоединить Azure SSIS IR к виртуальной сети, подключенной к локальной сети. Если каталог SSIS размещен в Базе данных SQL Azure, которая не находится в виртуальной сети, то необходимо открыть соответствующие порты. Если каталог SSIS размещен в управляемом экземпляре SQL Azure, который находится в классической виртуальной сети, то Azure SSIS IR можно присоединить к той же классической виртуальной сети или к другой классической виртуальной сети, имеющей подключение типа "классическая виртуальная сеть — классическая виртуальная сеть" с той сетью, в которой находится управляемый экземпляр SQL Azure. В следующих разделах приведены дополнительные сведения.

Необходимо учитывать следующие важные замечания. 

- Если нет существующей виртуальной сети, подключенной к локальной сети, то сначала создайте [классическую виртуальную сеть](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) для присоединения своей среды выполнения интеграции Azure SSIS. Затем настройте подключение через [VPN-шлюз](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) типа "сеть — сеть" между этой виртуальной сетью и вашей локальной сетью.
- Если есть существующая классическая виртуальная сеть, подключенная к локальной сети в том же расположении, что и ваша среда выполнения интеграции Azure SSIS, то вы можете присоединить к ней свою среду выполнения интеграции Azure SSIS.
- Если есть существующая классическая виртуальная сеть, подключенная к локальной сети в расположении, отличном от вашей среды выполнения интеграции Azure SSIS, то вы можете сначала создать [классическую виртуальную сеть](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) для присоединения своей среды выполнения интеграции Azure SSIS. Затем настройте подключение типа [классическая виртуальная сеть — классическая виртуальная сеть](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md).
- Если есть существующая виртуальная сеть Azure Resource Manager, подключенная к локальной сети, то сначала создайте [классическую виртуальную сеть](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) для присоединения своей среды выполнения интеграции Azure SSIS. Затем настройте подключение типа [классическая виртуальная сеть — виртуальная сеть Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Сервер служб доменных имен 
Если необходимо использовать собственный сервер служб доменных имен (DNS) в виртуальной сети, присоединенной средой выполнения интеграции Azure SSIS, то следуйте инструкциям этого руководства и [убедитесь, что узлы вашей среды выполнения интеграции Azure SSIS в виртуальной сети могут разрешать конечные точки Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Группа безопасности сети
Если необходимо реализовать группу безопасности сети (NSG) в виртуальной сети, присоединенной средой выполнения интеграции Azure SSIS, то разрешите входящий и исходящий трафик через следующие порты:

| порты; | Направление | Транспортный протокол | Назначение | Входящий источник/исходящее назначение |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | Входящий трафик | TCP | Службы Azure используют эти порты для взаимодействия с узлами вашей среды выполнения интеграции Azure SSIS в виртуальной сети. | Интернет | 
| 443 | Исходящие | TCP | Узлы среды выполнения интеграции Azure SSIS в виртуальной сети используют этот порт для доступа к службам Azure, например, к службе хранилища Azure, концентратору событий и т. д. | ИНТЕРНЕТ | 
| 1433<br/>11000–11999;<br/>14000–14999.  | Исходящие | TCP | Узлы среды выполнения интеграции Azure SSIS в виртуальной сети используют эти порты для доступа к базе данных SSISDB, размещенной на сервере Базы данных SQL Azure (это не относится к базе данных SSISDB, размещенной на управляемом экземпляре SQL Azure). | Интернет | 

## <a name="configure-vnet"></a>Настройка виртуальной сети
Сначала необходимо настроить виртуальную сеть, с помощью одного из следующих способов (скрипт vs. Портал Azure) перед присоединением IR Azure SSIS в виртуальной сети. 

### <a name="script-to-configure-vnet"></a>Настройка виртуальной сети с помощью сценария 
Добавьте следующий скрипт, чтобы автоматически настроить виртуальную сеть и параметры разрешений для выполнения интеграции вашего Azure SSIS для присоединения виртуальной сети.

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
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

### <a name="use-portal-to-configure-vnet"></a>Настройка виртуальной сети с помощью портала
Самым простым способом настройки виртуальной сети является выполнение сценария. Если у вас нет доступа к настройке виртуальной сети или не удается выполнить автоматическую настройку, то владелец этой виртуальной сети или вы можете попробовать настроить ее вручную, используя следующие шаги:

### <a name="find-the-resource-id-for-your-azure-vnet"></a>Найдите идентификатор ресурса для своей виртуальной сети Azure.
 
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
    2. На странице **Добавление разрешений** в поле **Роль** выберите **Участник классической виртуальной машины**. Скопируйте и вставьте **ddbf3205-c6bd-46ae-8127-60eb93363864** в **выберите** текстовое поле, а затем выберите **пакета Microsoft Azure** из списка результатов поиска. 
    
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

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Создать IR Azure SSIS и присоедините его к виртуальной сети
Можно создать IR Azure SSIS и присоединить ее к виртуальной сети в то же время. Полный сценарий и инструкции для создания служб SSIS Azure IR и присоединить ее к виртуальной сети в то же время, в разделе [создать IR Azure SSIS](create-azure-ssis-integration-runtime.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Присоединение существующей IR Azure SSIS к виртуальной сети
Скрипт в [создания Azure-интеграции среды выполнения служб SSIS](create-azure-ssis-integration-runtime.md) статьи показано, как создать IR Azure SSIS и присоедините его к виртуальной сети в тот же скрипт. При наличии существующего SSIS Azure, выполните следующие действия, чтобы присоединить ее к виртуальной сети. 

1. Остановка служб SSIS Azure IR.
2. Настройте IR Azure SSIS для присоединения виртуальной сети. 
3. Запуск служб SSIS Azure IR. 

## <a name="define-the-variables"></a>Определите переменные

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
# Get the following information from the properties page for your Classic Virtual Network in the Azure portal
# It should be in the format: 
# $VnetId = "/subscriptions/<Azure Subscription ID>/resourceGroups/<Azure Resource Group>/providers/Microsoft.ClassicNetwork/virtualNetworks/<Class Virtual Network Name>"
$VnetId = "<Name of your Azure classic virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Остановка служб SSIS Azure IR
Остановка выполнения интеграции служб SSIS Azure перед присоединением к виртуальной сети. Эта команда освобождает все узлы и прекращает выставление счетов.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Настройка параметров виртуальной сети для первоначальной репликации служб SSIS Azure для присоединения
Зарегистрируйтесь для поставщика ресурсов пакета Azure.

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

### <a name="configure-the-azure-ssis-ir"></a>Настройка служб SSIS Azure IR
Выполните команду Set-AzureRmDataFactoryV2IntegrationRuntime настройки интеграции среды выполнения служб SSIS Azure для присоединения виртуальной сети: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

## <a name="start-the-azure-ssis-ir"></a>Запуск служб SSIS Azure IR
Выполните следующую команду для запуска среды выполнения интеграции SSIS Azure: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Выполнение этой команды занимает от **20 до 30 минут**.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о среде выполнения Azure SSIS см. в следующих разделах: 

- [Среда выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). В этой статье содержатся общие сведения о средах выполнения интеграции в целом, включая Azure SSIS IR. 
- [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-deploy-ssis-packages-azure.md). Эта статья содержит пошаговые инструкции для создания Azure SSIS IR и использует базу данных SQL Azure для размещения каталога SSIS. 
- [Создание среды выполнения интеграции Azure SSIS](create-azure-ssis-integration-runtime.md). Эта статья дополняет соответствующее руководство, а также предоставляет инструкции по использованию управляемого экземпляра SQL Azure (закрытая предварительная версия) и присоединению среды выполнения интеграции к виртуальной сети. 
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этом статье показано, как извлечь сведения о среде выполнения интеграции Azure SSIS и описания состояний из возвращаемых данных. 
- [Управление средой выполнения интеграции Azure SSIS](manage-azure-ssis-integration-runtime.md). В этой статье показано, как остановить, запустить или удалить Azure SSIS IR. В ней также показано, как развернуть Azure SSIS IR путем добавления дополнительных узлов в среду выполнения интеграции. 
