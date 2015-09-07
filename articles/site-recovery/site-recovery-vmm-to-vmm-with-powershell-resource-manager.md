<properties
	pageTitle="Защита виртуальных машин между двумя сайтами VMM с помощью PowerShell и диспетчера ресурсов Microsoft Azure"
	description="Автоматизируйте защиту между двумя локальными сайтами VMM и Azure с помощью PowerShell и диспетчера ресурсов Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  Использование PowerShell и диспетчера ресурсов Azure между двумя сайтами VMM


## Обзор

Служба Azure Site Recovery помогает реализовать стратегии непрерывности бизнеса и восстановления после сбоев (BCDR), управляя процессами репликации, отработки отказа и восстановления виртуальных машин в ряде сценариев развертывания. Полный список сценариев развертывания приведен в статье [Обзор Site Recovery](site-recovery-overview.md).

Azure PowerShell — это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Он может работать с двумя типами модулей: модулем профиля Azure или модулем диспетчера ресурсов Azure (ARM).

В этой статье описано, как использовать Windows PowerShell® в сочетании с ARM для развертывания службы Azure Site Recovery с целью настройки и контроля защиты виртуальных машин между двумя сайтами VMM. Репликация и отработка отказов виртуальных машин, работающих на серверах узлов Hyper-V, размещенных в частных облаках VMM на основном сайте, будет выполняться на дополнительный сайт VMM с помощью реплики Hyper-V.

Для использования этой статьи вам не потребуется большой опыт работы с PowerShell, но предполагается, что вам знакомы основные понятия, такие как модули, командлеты и сеансы. Дополнительные сведения о Windows PowerShell можно найти в разделе [Начало работы с Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx). Подробнее ознакомьтесь с разделом [Использование Azure PowerShell с диспетчером ресурсов Azure](powershell-azure-resource-manager.md).

Эта статья включает перечень необходимых для сценария компонентов и инструкции по настройке Azure PowerShell для работы со службой Site Recovery, созданию хранилища Site Recovery, установке поставщика Azure Site Recovery на серверах VMM и их регистрации в хранилище, настройке параметров защиты для облаков VMM, которые будут применяться ко всем защищенным виртуальным машинам, и включению защиты для этих виртуальных машин. В конце вы выполните отработку отказа, чтобы убедиться, что все работает правильно.


## Перед началом работы

Убедитесь, что выполнены следующие предварительные требования.

- Вам потребуется учетная запись [Microsoft Azure](http://azure.microsoft.com/). Вам потребуется учетная запись [Microsoft Azure](http://azure.microsoft.com/). Вы можете начать с [бесплатной пробной версии](pricing/free-trial/). Вы также можете ознакомиться с разделом [Расценки на службу восстановления сайтов Azure](http://azure.microsoft.com/pricing/details/site-recovery/).
- Вам потребуется сервер VMM на основном и дополнительном сайтах под управлением System Center 2012 R2.
- У каждого сервера VMM должно быть хотя бы одно облако, содержащее следующие компоненты:
	- одну или несколько групп узлов VMM;
	- один или несколько серверов узлов или кластеров Hyper-V в каждой группе узлов;
	- одну или несколько виртуальных машин на исходном сервере Hyper-V.
	- Дополнительные сведения о настройке облаков VMM

		- [Новые функции частного облака в System Center 2012 R2 VMM](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA) и [VMM 2012 и облачные среды](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
		- [Настройка структуры облака VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric).
		- [Создание частного облака в VMM](https://technet.microsoft.com/library/jj860425.aspx) и [Пошаговое руководство: создание частных облаков с помощью VMM в System Center 2012 с пакетом обновления 1 (SP1)](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
- Один или несколько серверов Hyper-V должны работать под управлением Windows Server 2012 (или более поздних версий) с ролью Hyper-V и установленными последними обновлениями. Сервер или кластер должен быть включен в облако VMM.
- Если Hyper-V выполняется в кластере, учтите, что брокер кластера не создается автоматически при использовании кластера на основе статических IP-адресов. Вам потребуется настроить брокер кластера вручную. Инструкции можно найти в статье [Настройка брокера реплики Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).

	- Вам потребуется Azure PowerShell. Убедитесь, что используете Azure PowerShell версии 0.9.6 или более поздней версии. Ознакомьтесь со статей [Установка и настройка Azure PowerShell](powershell-install-configure.md). 
	- При установке Azure PowerShell также устанавливается специальная консоль. Вы можете выполнять команды PowerShell через эту консоль или любую другую основную программу, такую как интегрированная среда сценариев Windows PowerShell.

## Шаг 1. Настройка PowerShell


1. Откройте консоль PowerShell и выполните следующую команду, чтобы переключиться на модуль ARM:

    `Switch-AzureMode AzureResourceManager`

3. Теперь выполните эту команду, чтобы добавить свою учетную запись Azure в сеанс PowerShell. Командлет запросит учетные данные входа для вашей учетной записи.

    `Add-AzureAccount`

	Обратите внимание, что если вы — партнер-поставщик облачных услуг, работающий от имени клиента, вам потребуется указать заказчика в качестве клиента при добавлении учетной записи Azure:

    `Add-AzureAccount-Tenant "customer"`

5. В учетной записи может быть несколько подписок, поэтому вам потребуется связать подписку, которую вы собираетесь использовать с учетной записью.

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. При первом использовании командлетов Site Recovery в рамках подписки вам потребуется зарегистрировать поставщика Azure для службы Site Recovery:

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## Шаг 2. Настройка хранилища Site Recovery

1. Если у вас еще нет хранилища Site Recovery, создайте его, выполнив командлет [New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx):

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## Шаг 3. Создание ключа регистрации хранилища

1. Выполните командлет [Get-AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx), чтобы получить ключ регистрации хранилища. Этот ключ потребуется вам для регистрации серверов VMM в хранилище:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## Шаг 4. Установка поставщика Azure Site Recovery

1. Скачайте файл установки поставщика со страницы быстрого запуска в хранилище Site Recovery.
2. На каждом сервере VMM создайте папку с помощью следующей команды:

    `pushd C:\ASR`

3. Выполните эту команду, чтобы извлечь файлы из скачанного поставщика:

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. Установите поставщика, выполнив следующую команду:

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. Дождитесь завершения установки, после чего зарегистрируйте сервер в хранилище:

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## Шаг 5. Настройка контекста хранилища

1. Выполните командлет Get-AzureSiteRecoveryVault, чтобы обеспечить выполнение всех команд в определенном хранилище:

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. Скачайте параметры хранилища:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. Чтобы обеспечить выполнение командлетов в хранилище, выполните следующую команду:

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## Шаг 3. Настройка параметров защиты облаков

После регистрации сервера VMM в хранилище вы можете настроить параметры защиты облаков, которые будут применяться ко всем виртуальным машинам на узлах Hyper-V, размещенных в облаках на зарегистрированном сервере VMM.

1. Создайте контейнер в хранилище для основного и дополнительного облаков:

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. Настройке параметры защиты для облаков:

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. Запустите задание привязки облачных контейнеров к параметрам защиты облаков:

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## Шаг 4. Включение защиты виртуальных машин

Включите защиту виртуальных машин в облаках VMM:

1. Получите виртуальную машину, которую требуется защитить:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. Включите защиту для нее:

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## Шаг 5. Запуск тестовой отработки отказа

1.	Выберите виртуальную машину, для которой требуется выполнить отработку отказа:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. Запустите задание тестовой отработки отказа:

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. Убедитесь, что выбранная виртуальная машина отображается на дополнительном сайте, и завершите отработку отказа:

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## Дальнейшие действия

Вопросы и замечания по этому сценарию вы можете оставить на [форуме службы Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/)

<!---HONumber=August15_HO9-->