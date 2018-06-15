---
title: Репликация виртуальных машин Hyper-V из облаков VMM с помощью Azure Site Recovery и PowerShell (Resource Manager) | Документация Майкрософт
description: Репликация виртуальных машин Hyper-V в облаках VMM с помощью Azure Site Recovery и PowerShell
services: site-recovery
documentationcenter: ''
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: rajanaki
ms.openlocfilehash: 67d9164fcf8fb3731c4e3a9d30ac5c38da5908c7
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677756"
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Репликация виртуальных машин Hyper-V из облаков VMM в Azure с помощью PowerShell и Azure Resource Manager
> [!div class="op_single_selector"]
> * [портале Azure](site-recovery-vmm-to-azure.md)
> * [PowerShell — Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell — классическая модель](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Обзор
Служба Azure Site Recovery помогает реализовать стратегии непрерывности бизнеса и восстановления после сбоев (BCDR), управляя процессами репликации, отработки отказа и восстановления виртуальных машин в ряде сценариев развертывания. Полный список сценариев развертывания приведен в статье [Обзор Site Recovery](site-recovery-overview.md).

В этой статье показано, как использовать PowerShell для автоматизации распространенных задач, которые необходимо выполнять при настройке Azure Site Recovery для репликации виртуальных машин Hyper-V в облаках VMM System Center в службу хранилища Azure.

В этой статье рассматриваются необходимые условия для реализации сценария, а также описываются следующие действия.

* Настройка хранилища служб восстановления.
* Установка на исходном сервере VMM поставщика Azure Site Recovery.
* Регистрация сервера в хранилище и добавление учетной записи хранения Azure.
* Установка агента служб восстановления Azure на серверах узлов Hyper-V.
* Настройка параметров защиты для облаков VMM, которые будут применяться ко всем защищенным виртуальным машинам.
* Включение защиты для таких виртуальных машин.
* Проверка отработки отказа, чтобы убедиться в надлежащей работе всех компонентов.

Если у вас возникают проблемы, опубликуйте свои вопросы на [форуме по Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания на основе диспетчера ресурсов.
>
>

## <a name="before-you-start"></a>Перед началом работы
Убедитесь, что выполнены следующие предварительные требования.

### <a name="azure-prerequisites"></a>Предварительные требования Azure
* Вам потребуется учетная запись [Microsoft Azure](https://azure.microsoft.com/) . Если у вас ее нет, воспользуйтесь [бесплатной учетной записью](https://azure.microsoft.com/free). Вы также можете ознакомиться с [расценками на использование диспетчера восстановления Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
* Чтобы опробовать репликацию в подписку CSP, вам потребуется подписка CSP. Дополнительные сведения о программе CSP см. в статье [Как зарегистрироваться в программе CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
* Для хранения данных, реплицируемых в Azure, потребуется учетная запись хранения Azure версии 2 (Resource Manager). На учетной записи необходимо включить георепликацию. Она должна находиться в том же регионе, что и служба Azure Site Recovery, и быть связана с той же подпиской или с подпиской CSP. Дополнительные сведения о настройке службы хранилища Azure см. в разделе [Введение в хранилище Microsoft Azure](../storage/common/storage-introduction.md).
* Нужно убедиться, что защищаемые виртуальные машины соответствуют [предварительным требованиям к виртуальным машинам Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

> [!NOTE]
> В настоящее через Powershell доступны только операции уровня виртуальной машины. Скоро будет включена поддержка операций на уровне плана восстановления.  Сейчас отработка отказа ограничена уровнем "защищенных виртуальных машин", на уровне плана восстановления она недоступна.
>
>

### <a name="vmm-prerequisites"></a>Предварительные требования VMM
* Вам потребуется сервер VMM под управлением System Center 2012 R2.
* На всех серверах VMM, содержащих виртуальные машины, для которых предполагается настроить защиту, должен выполняться поставщик Azure Site Recovery. Он устанавливается во время развертывания Azure Site Recovery.
* Требуется по крайней мере одно облако на сервере VMM, для которого предполагается настроить защиту. Облако должно содержать:
  * одну или несколько групп узлов VMM;
  * Один или несколько серверов или кластеров узлов Hyper-V в каждой группе узлов.
  * одну или несколько виртуальных машин на исходном сервере Hyper-V.
* Дополнительные сведения о настройке облаков VMM
  * Дополнительные сведения о частных облаках VMM см. в разделах [Новые функции частного облака в System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) и [VMM 2012 и облачные среды](http://go.microsoft.com/fwlink/?LinkId=324956).
  * Ознакомьтесь со статьей [Настройка структуры облака VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * После того как все элементы структуры облака будут готовы, ознакомьтесь с дополнительными сведениями о создании частных облаков в разделах [Создание частного облака в VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) и [Пошаговое руководство: создание частных облаков с VMM System Center 2012 SP1](http://go.microsoft.com/fwlink/p/?LinkId=324954).

### <a name="hyper-v-prerequisites"></a>Предварительные требования Hyper-V
* Серверы узлов Hyper-V должны работать как минимум под управлением **Windows Server 2012** с ролью Hyper-V или **Microsoft Hyper-V Server 2012** и содержать последние обновления.
* Если Hyper-V выполняется в кластере, учтите, что брокер кластера не создается автоматически при использовании кластера на основе статических IP-адресов. Вам потребуется настроить брокер кластера вручную. Для
* Инструкции см. в статье [How to Configure Hyper-V Replica Broker](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx) (Как настроить брокер реплики Hyper-V).
* Все кластеры или серверы узлов Hyper-V, для которых вы настраиваете управление защитой, должны быть включены в облако VMM.

### <a name="network-mapping-prerequisites"></a>Предварительные требования сетевого сопоставления
Когда в Azure включена защита виртуальных машин, с помощью сопоставления сетей осуществляется сопоставление сетей виртуальных машин на исходном сервере VMM и целевых сетей Azure, что обеспечивает следующие преимущества.

* Все компьютеры, перешедшие в состояние отказа в одной и той же сети, могут подключиться друг к другу, независимо от назначенного им плана восстановления.
* При наличии сетевого шлюза в целевой сети Azure виртуальные машины могут подключиться к другим локальным виртуальным машинам.
* Если сопоставление не включено, то после отработки отказа в Azure друг к другу смогут подключаться только те виртуальные машины, которые отрабатывают отказ в рамках одного плана восстановления.

Для развертывания сетевого сопоставления требуется следующее.

* Защищаемые виртуальные машины на исходном сервере VMM должны быть подключены к сети виртуальных машин. Эта сеть должна быть подключена к логической сети, которая связана с облаком.
* Сеть Azure, к которой смогут подключаться реплицированные виртуальные машины после отработки отказа. Эта сеть выбирается во время отработки отказа. Сеть должна располагаться в том же регионе, что и ваша подписка Azure Site Recovery.

Дополнительные сведения о сетевом сопоставлении см. в следующих статьях.

* [Настройка логических сетей в VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Настройка сетей виртуальных машин и шлюзов в VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [Настройка и мониторинг виртуальных сетей в Azure](https://azure.microsoft.com/documentation/services/virtual-network/)

### <a name="powershell-prerequisites"></a>Необходимые компоненты PowerShell
Перед началом работы убедитесь, что среда Azure PowerShell готова к работе. Если вы уже используете PowerShell, необходимо выполнить обновление до версии 0.8.10 или более поздней версии. Дополнительные сведения о настройке PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). После установки и настройки PowerShell все доступные командлеты для службы можно просмотреть [здесь](/powershell/azure/overview).

Дополнительные сведения об использовании командлетов, например о типовой обработке значений параметров, входных и выходных параметров в Azure PowerShell, приведены в статье [Начало работы с командлетами Azure](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Шаг 1. Настройка подписки
1. В Azure Powershell войдите в свою учетную запись Azure с помощью следующих командлетов:

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Получите список своих подписок. При этом также отобразится список идентификаторов subscriptionID для каждой подписки. Запишите идентификатор subscriptionID подписки, в которой хотите создать хранилище служб восстановления.

        Get-AzureRmSubscription
3. Задайте подписку, в которой будет создано хранилище служб восстановления, указав идентификатор подписки.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Шаг 2. Создание хранилища служб восстановления
1. Если у вас еще нет группы ресурсов в Azure Resource Manager, создайте ее.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Создайте новое хранилище служб восстановления и сохраните созданный объект хранилища ASR в переменной, которая будет использоваться далее. Объект хранилища ASR также можно получить после создания с помощью командлета Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Шаг 3. Настройка контекста для хранилища служб восстановления

Задайте контекст хранилища, выполнив указанную ниже команду.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Шаг 4. Установка поставщика Azure Site Recovery
1. На компьютере с VMM создайте каталог, выполнив следующую команду:

       New-Item c:\ASR -type directory
2. Распакуйте файлы с помощью загруженного поставщика, выполнив следующую команду:

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Используйте следующую команду для установки провайдера:

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   Дождитесь завершения процесса установки.
4. Зарегистрируйте сервер в хранилище, используя следующую команду:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-an-azure-storage-account"></a>Шаг 5. Создание учетной записи хранения Azure

Если у вас нет учетной записи хранения Azure, создайте учетную запись с включенной георепликацией в том же регионе, что и хранилище, выполнив следующую команду:

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Обратите внимание, что учетная запись хранения должна находиться в том же регионе, что и служба Azure Site Recovery, и должна быть связана с той же подпиской.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Шаг 6. Установка агента служб восстановления Azure
1. С портала [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) загрузите агент служб восстановления Azure и установите его на всех серверах узлов Hyper-V в облаках VMM, которые нужно защитить.
2. Выполните следующую команду на всех узлах VMM:

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Шаг 7. Настройка параметров защиты облачных систем
1. Создайте политику репликации в Azure, выполнив следующую команду:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. Получите контейнер защиты, выполнив следующие команды:

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. Извлеките сведения о политике в переменную с помощью созданного задания, указав понятное имя политики:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Свяжите контейнер защиты с политикой репликации:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. После завершения задания выполните следующую команду:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. После завершения обработки задания выполните следующую команду:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Для проверки выполнения операции выполните действия, описанные в разделе [Мониторинг активности](#monitor).

## <a name="step-8-configure-network-mapping"></a>Шаг 8. Настройка сетевого сопоставления
Прежде чем приступать к настройке сетевого сопоставления, убедитесь, что виртуальные машины на исходном сервере VMM подключены к сети виртуальных машин. Кроме того, создайте одну или несколько виртуальных сетей Azure.

Дополнительные сведения о создании виртуальных сетей с помощью Azure Resource Manager и PowerShell см. в статье [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью PowerShell и Azure Resource Manager](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

Обратите внимание, что с одной сетью Azure можно сопоставить несколько сетей виртуальных машин. Если в целевой сети имеется несколько подсетей и одна из этих подсетей называется так же, как и подсеть, в которой размещается исходная виртуальная машина, то после отработки отказа реплика виртуальной машины будет подключаться к этой целевой подсети. Если целевой подсети с таким же именем нет, то виртуальная машина будет подключаться к первой подсети в сети.

1. Первая команда возвращает серверы для текущего хранилища Azure Site Recovery. Команда сохраняет серверы Microsoft Azure Site Recovery в массиве $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Вторая команда получает сеть восстановления сайта для первого сервера в массиве $Servers. Команда сохраняет сети в переменной $Networks.

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. Третья команда извлекает виртуальные сети Azure и затем сохраняет это значение в переменной $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. Последний командлет создает сопоставление между основной сетью и сетью виртуальной машины Azure. Командлет устанавливает основную сеть в качестве первого элемента $Networks. Командлет делает сеть виртуальных машин первым элементом в $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>Шаг 9. Включение защиты для виртуальных машин
После успешной настройки серверов, облаков и сетей можно включить защиту для виртуальных машин в облаке.

 Обратите внимание на следующее.

* Виртуальные машины должны соответствовать требованиям, предъявляемым Azure. Эти требования приведены в разделе [Предварительные условия и поддержка](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) руководства по планированию.
* Чтобы включить защиту, необходимо задать соответствующие свойства операционной системы и диска с операционной системой для виртуальной машины. Данные свойства можно установить при создании виртуальной машины в VMM с помощью шаблона виртуальной машины. Эти свойства также можно задать для существующих виртуальных машин на вкладках **Общие** и **Конфигурация оборудования** свойств виртуальной машины. Если установка этих свойств в VMM не была выполнена, сделать это можно на портале Azure Site Recovery.

1. Чтобы включить защиту, выполните следующую команду для получения контейнера защиты:

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. Получите объект защиты (виртуальную машину), выполнив следующую команду:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. Включите аварийное восстановление для виртуальной машины, выполнив следующую команду:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>Выполните тестирование развертывания
Чтобы проверить развертывание, можно запустить тестовую отработку отказа для отдельной виртуальной машины. Также можно создать план восстановления, состоящий из нескольких виртуальных машин, и запустить тестовую отработку отказа в плане. Тестовая обработка отказа имитирует механизм отработки отказа и восстановления в изолированной сети. Обратите внимание на следующее.

* Чтобы подключиться к виртуальной машине в Azure с использованием удаленного рабочего стола после отработки отказа, включите подключение удаленного рабочего стола на виртуальной машине до запуска тестовой отработки отказа.
* После отработки отказа для подключения к виртуальной машине в Azure с использованием удаленного рабочего стола будет использоваться общедоступный IP-адрес. Если необходимо это сделать, убедитесь в отсутствии политик домена, которые не допустили бы подключение к виртуальной машине с использованием общедоступного адреса.

Для проверки выполнения операции выполните действия, описанные в разделе [Мониторинг активности](#monitor).

### <a name="run-a-test-failover"></a>Запуск тестовой отработки отказа
- Запустите тестовую обработку отказа с помощью следующей команды:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Запуск запланированной отработки отказа
- Запустите запланированную обработку отказа с помощью следующей команды:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Запуск незапланированной отработки отказа
- Запустите внеплановую обработку отказа с помощью следующей команды:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

## <a name=monitor></a> Мониторинг активности
Используйте следующие команды для мониторинга активности. Обратите внимание, что между обработкой заданий необходимы паузы для ожидания завершения обработки.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Дополнительная информация
[Узнайте больше](/powershell/module/azurerm.recoveryservices.backup/#recovery) об использовании командлетов PowerShell инструмента Azure Resource Manager для службы Azure Site Recovery.
