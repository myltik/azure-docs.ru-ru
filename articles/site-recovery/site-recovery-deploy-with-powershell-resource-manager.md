---
title: "Репликация виртуальных машин Hyper-V с помощью PowerShell и Azure Resource Manager | Документация Майкрософт"
description: "Автоматизируйте репликацию виртуальных машин Hyper-V в Azure с помощью Azure Site Recovery, используя PowerShell и Azure Resource Manager."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: 
ms.assetid: 05e0d76e-c3f5-4845-8052-094019b6d102
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: dbd562b73b0caecd0feb993bd6fb796dddb0438c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Репликация между локальными виртуальными машинами Hyper-V и Azure с помощью PowerShell и Azure Resource Manager
> [!div class="op_single_selector"]
> * [Портал Azure](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell — Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
> * [Классический портал](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

## <a name="overview"></a>Обзор
Служба Azure Site Recovery помогает реализовать стратегии непрерывности бизнеса и восстановления после сбоев, управляя процессами репликации, отработки отказа и восстановления виртуальных машин в ряде сценариев развертывания. Полный список сценариев развертывания приведен в статье [Обзор Site Recovery](site-recovery-overview.md).

Azure PowerShell — это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Он может работать с двумя типами модулей: модулем профиля Azure или модулем диспетчера ресурсов Azure.

Командлеты PowerShell для службы Site Recovery, доступные в Azure PowerShell для диспетчера ресурсов Azure, позволяют обеспечить защиту и выполнить восстановление серверов в Azure.

В этой статье описано, как использовать Windows PowerShell® в сочетании с Azure Resource Manager, чтобы развернуть службу Site Recovery для настройки и контроля защиты серверов в Azure. Здесь показано, как обеспечить защиту, выполнить отработку отказа и восстановление виртуальных машин, работающих на узле Hyper-V, в Azure с помощью Azure PowerShell в сочетании с Azure Resource Manager.

> [!NOTE]
> Командлеты PowerShell для службы Site Recovery в настоящее время позволяют настроить следующее: изменить один сайт диспетчера виртуальных машин на другой, сайт диспетчера виртуальных машин на Azure и сайт Hyper-V на Azure.
>
>

Для работы с этой статьей не требуется большой опыт работы с PowerShell, но необходимо знакомство с основными понятиями, такими как модули, командлеты и сеансы. Дополнительные сведения о Windows PowerShell можно найти в разделе [Начало работы с Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Дополнительные сведения см. также в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Партнеры корпорации Майкрософт, которые участвуют в программе для поставщиков облачных решений, могут настроить защиту серверов своих заказчиков и управлять ею в рамках соответствующей (клиентской) подписки участника этой программы.
>
>

## <a name="before-you-start"></a>Перед началом работы
Убедитесь, что выполнены следующие предварительные требования.

* Учетная запись [Microsoft Azure](https://azure.microsoft.com/) . Начните с [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/). Также вы можете ознакомиться с [расценками на использование менеджера Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Сведения об этом выпуске и его установке см. в разделе [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* Модули [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) и [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). Последние версии этих модулей можно получить из [коллекции PowerShell](https://www.powershellgallery.com/)

В этой статье показано, как использовать Azure Powershell в сочетании с Azure Resource Manager для настройки защиты серверов и управления этой защитой. В приведенном в этой статье примере показано, как защитить виртуальную машину, которая выполняется на узле Hyper-V в Azure. В этом примере действуют указанные ниже требования. Подробный набор требований для различных сценариев с использованием службы Site Recovery можно просмотреть в документации по соответствующему сценарию.

* Узел Hyper-V под управлением Windows Server 2012 R2 или Microsoft Hyper-V Server 2012 R2, содержащий одну или несколько виртуальных машин.
* Серверы Hyper-V должны быть подключены к Интернету напрямую или через прокси-сервер.
* Виртуальные машины, которые требуется защитить, должны отвечать [предварительным требованиям к виртуальным машинам](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="step-1-sign-in-to-your-azure-account"></a>Шаг 1. Вход в учетную запись Azure
1. Откройте консоль PowerShell и выполните следующую команду, чтобы войти в учетную запись Azure. Этот командлет открывает веб-страницу с запросом на ввод учетных данных.

        Login-AzureRmAccount

    Можно также указать учетные данные для учетной записи в качестве параметра в командлете `Login-AzureRmAccount`. Для этого используется параметр `-Credential`.

    Если вы — партнер-поставщик облачных услуг, работающий от имени клиента, вам потребуется указать заказчика в качестве клиента. Для этого нужно ввести идентификатор или основное доменное имя клиента.

        Login-AzureRmAccount -Tenant "fabrikam.com"
2. В учетной записи может быть несколько подписок, поэтому вам потребуется связать подписку, которую вы собираетесь использовать, с учетной записью.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName
3. Убедитесь, что в вашей подписке зарегистрированы поставщики Azure для служб восстановления и службы Site Recovery, выполнив следующие команды:

   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

   Если в выходных данных этих команд для параметра **RegistrationState** установлено значение **Registered**, можно переходить к шагу 2. В противном случае необходимо зарегистрировать отсутствующий поставщик в своей подписке.

   Чтобы зарегистрировать поставщик Azure в службе Site Recovery и службах восстановления, выполните следующие команды.

       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

   Убедитесь, что поставщики успешно зарегистрированы, выполнив команды `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` и `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-recovery-services-vault"></a>Шаг 2. Настройка хранилища служб восстановления
1. Создайте для диспетчера ресурсов Azure группу ресурсов, в которой будет создано хранилище, или воспользуйтесь существующей группой ресурсов. Чтобы создать группу ресурсов, можно выполнить следующую команду:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    Здесь переменная $ResourceGroupName содержит имя создаваемой группы ресурсов, а $Geo — регион Azure, в котором ее необходимо создать (например, Южная Бразилия).

    Список групп ресурсов в подписке можно получить с помощью командлета `Get-AzureRmResourceGroup` .
2. Создайте хранилище служб восстановления Azure следующим образом:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Получить список существующих хранилищ можно с помощью командлета `Get-AzureRmRecoveryServicesVault`.

> [!NOTE]
> Если требуется выполнять операции с хранилищами Site Recovery, созданными с помощью классического портала или модуля PowerShell управления службами Azure, список таких хранилищ можно получить с помощью командлета `Get-AzureRmSiteRecoveryVault`. Для каждой новой операции рекомендуется создавать отдельное хранилище служб восстановления. Хранилища Site Recovery, созданные ранее, по-прежнему поддерживаются, но не включают новые функции.
>
>

## <a name="step-3-set-the-recovery-services-vault-context"></a>Шаг 3. Настройка контекста для хранилища служб восстановления
1. Задайте контекст хранилища, выполнив следующие команды:

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Шаг 4. Создание сайта Hyper-V и ключа регистрации хранилища для сайта
1. Создайте сайт Hyper-V следующим образом:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Этот командлет запускает задание Site Recovery для создания сайта и возвращает объект этого задания. Дождитесь завершения задания и убедитесь, что оно выполнено успешно.

    Вы можете получить объект задания и тем самым проверить текущее состояние задания с помощью командлета Get-AzureRmSiteRecoveryJob.
2. Создайте и скачайте ключ регистрации для сайта, выполнив следующие действия:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Скопируйте скачанный ключ на узел Hyper-V. Этот ключ вам потребуется, чтобы зарегистрировать узел Hyper-V на сайте.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Шаг 5. Установка поставщика Azure Site Recovery и агента служб восстановления Azure на узле Hyper-V
1. Скачайте установщик последней версии поставщика с веб-сайта [корпорации Майкрософт](https://aka.ms/downloaddra).
2. Запустите установщик на узле Hyper-V. По завершении установки перейдите к шагу регистрации.
3. При появлении запроса укажите скачанный ключ регистрации сайта и завершите регистрацию узла Hyper-V на сайте.
4. Убедитесь, что узел Hyper-V зарегистрирован на сайте, с помощью следующей команды:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Шаг 6. Создание политики репликации и связывание ее с контейнером защиты
1. Создайте политику репликации, выполнив следующую команду:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Проверьте возвращенный результат задания, чтобы убедиться, что политика репликации успешно создана.

   > [!IMPORTANT]
   > Указанная учетная запись хранения должна находиться в том же регионе Azure, что и хранилище служб восстановления. В этой учетной записи также необходимо включить функцию георепликации.
   >
   > * Если тип указанной учетной записи хранения Site Recovery — служба хранилища Azure (классический способ), отработка отказа защищенной машины будет выполнена с переносом в Azure IaaS (классический способ).
   > * Если тип указанной учетной записи хранения Site Recovery — хранилище Azure (диспетчер ресурсов Azure), при обработке отказа защищенных машин машина будет восстановлена в Azure IaaS (диспетчер ресурсов Azure).
   >
   >
2. Получите соответствующий сайту контейнер защиты, выполнив следующую команду:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Свяжите контейнер защиты с политикой репликации, выполнив следующую команду

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer.

   Дождитесь завершения этого задания и убедитесь, что оно выполнено успешно.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Шаг 7. Включение защиты для виртуальных машин
1. Получите объект защиты, соответствующий виртуальной машине, которую необходимо защитить, выполнив следующую команду:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Включите защиту виртуальной машины, выполнив следующую команду:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

   > [!IMPORTANT]
   > Указанная учетная запись хранения должна находиться в том же регионе Azure, что и хранилище служб восстановления. В этой учетной записи также необходимо включить функцию георепликации.
   >
   > * Если тип указанной учетной записи хранения Site Recovery — служба хранилища Azure (классический способ), отработка отказа защищенной машины будет выполнена с переносом в Azure IaaS (классический способ).
   > * Если тип указанной учетной записи хранения Site Recovery — хранилище Azure (диспетчер ресурсов Azure), при обработке отказа защищенных машин машина будет восстановлена в Azure IaaS (диспетчер ресурсов Azure).
   >
   > Если к виртуальной машине, которую необходимо защитить, подключено несколько дисков, нужно указать диск операционной системы (ОС) с помощью параметра *OSDiskName* .
   >
   >
3. Дождитесь, пока виртуальная машина перейдет в защищенное состояние после начальной репликации. Это может занять некоторое время в зависимости от таких факторов, как объем данных для репликации и доступная пропускная способность вышестоящих служб в Azure. Когда виртуальная машина переходит в защищенное состояние, состояние задания и описание состояния обновляются следующим образом:

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Обновите свойства восстановления, такие как размер роли виртуальной машины и сеть Azure, чтобы подключить сетевые карты виртуальной машины после отработки отказа.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Шаг 8. Запуск тестовой отработки отказа
1. Запустите задание тестовой отработки отказа, выполнив следующую команду:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Убедитесь, что тестовая виртуальная машина создана в Azure. (После создания тестовой виртуальной машины в Azure задание тестовой отработки отказа будет приостановлено. Когда задание будет возобновлено, как показано на следующем шаге, будут очищены все созданные артефакты. После этого задание будет завершено.)
3. Завершите тестовую отработку отказа, выполнив следующую команду:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Дальнейшие действия
[Узнайте больше](https://msdn.microsoft.com/library/azure/mt637930.aspx) о командлетах PowerShell инструмента Azure Resource Manager для службы Azure Site Recovery.
