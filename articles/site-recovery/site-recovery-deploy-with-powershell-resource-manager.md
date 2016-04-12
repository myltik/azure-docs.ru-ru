<properties
	pageTitle="Защита серверов в Azure с помощью Azure PowerShell в сочетании с диспетчером ресурсов Azure | Microsoft Azure"
	description="Автоматизируйте защиту серверов в Azure с помощью Azure Site Recovery, используя PowerShell в сочетании с диспетчером ресурсов Azure."
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="03/16/2016"
	ms.author="bsiva"/>

# Развертывание службы Azure Site Recovery с использованием PowerShell и диспетчера ресурсов Azure

> [AZURE.SELECTOR]
- [Классический портал Azure](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell — Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)



## Обзор

Служба Azure Site Recovery помогает реализовать стратегии непрерывности бизнеса и восстановления после сбоев (BCDR), управляя процессами репликации, отработки отказа и восстановления виртуальных машин в ряде сценариев развертывания. Полный список сценариев развертывания приведен в статье [Обзор Site Recovery](site-recovery-overview.md).

Azure PowerShell — это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Он может работать с двумя типами модулей: модулем профиля Azure или модулем диспетчера ресурсов Azure (ARM).

Командлеты PowerShell для Azure Site Recovery (ASR), доступные в Azure PowerShell для ARM, позволяют обеспечить защиту и выполнить восстановление серверов в Azure.

В этой статье описано, как использовать Windows PowerShell® в сочетании с ARM, чтобы развернуть службу Azure Site Recovery для настройки и контроля защиты серверов в Azure на определенном примере. Здесь показано, как обеспечить защиту, выполнить отработку отказа и восстановление виртуальных машин, работающих на узле Hyper-V, в Azure с помощью Azure PowerShell в сочетании с ARM.

> [AZURE.NOTE] В настоящее время с помощью командлетов PowerShell для Azure Site Recovery можно настроить сценарии восстановления между сайтами VMM, с сайта VMM в Azure и с сайта Hyper-V в Azure. Вскоре будет добавлена поддержка и других сценариев с использованием ASR.

Для использования этой статьи вам не потребуется большой опыт работы с PowerShell, но предполагается, что вам знакомы основные понятия, такие как модули, командлеты и сеансы. Дополнительные сведения о Windows PowerShell см. в разделе [Начало работы с Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).
- См. раздел [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).


## Основные возможности

- Вы можете защитить серверы в Azure, а также выполнить для них отработку отказа и восстановление в Azure IaaS (с использованием диспетчера ресурсов Azure) или в Azure IaaS (классический способ).
- Партнеры корпорации Майкрософт, которые участвуют в программе для поставщиков облачных решений, могут настроить защиту серверов своих заказчиков и управлять ею в рамках подписки участника этой программы (клиентской подписки).

## Перед началом работы

Убедитесь, что выполнены следующие предварительные требования.

- Вам потребуется учетная запись [Microsoft Azure](https://azure.microsoft.com/). Начните с [бесплатной пробной версии](pricing/free-trial/). Также вы можете ознакомиться с [расценками на использование менеджера Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Вам потребуется Azure PowerShell 1.0. Сведения об этом выпуске и его установке см. в разделе [Azure PowerShell 1.0](https://azure.microsoft.com/).
- Необходимо установить модули [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) и [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). Последние версии этих модулей можно получить из [коллекции PowerShell](https://www.powershellgallery.com/).

В этой статье показано, как использовать Azure Powershell в сочетании с ARM для настройки защиты серверов и управления ею на конкретном примере. Здесь описано, как защитить виртуальную машину, работающую на узле Hyper-V, в Azure, и изложены соответствующие предварительные требования. Подробный набор требований для различных сценариев с использованием ASR можно просмотреть в документации по соответствующему сценарию.

- Вам потребуется узел Hyper-V под управлением Windows Server 2012 R2, содержащий одну или несколько виртуальных машин.
- Серверы Hyper-V должны быть подключены к Интернету напрямую или через прокси-сервер.
- Виртуальные машины, которые требуется защитить, должны отвечать [предварительным требованиям к виртуальным машинам](site-recovery-best-practices.md#virtual-machines).
	

## Шаг 1. Вход в свою учетную запись Azure


1. Откройте консоль PowerShell и выполните следующую команду, чтобы войти в учетную запись Azure. Этот командлет открывает веб-страницу с запросом на ввод учетных данных.

    	Login-AzureRmAccount

	Можно также указать учетные данные для учетной записи в качестве параметра в командлете `Login-AzureRmAccount`. Для этого используется параметр `-Credential`.
	
	Если вы — партнер-поставщик облачных услуг, работающий от имени клиента, вам потребуется указать заказчика в качестве клиента. Для этого нужно ввести идентификатор или основное доменное имя клиента.

		Login-AzureRmAccount -Tenant "fabrikam.com"

2. В учетной записи может быть несколько подписок, поэтому вам потребуется связать подписку, которую вы собираетесь использовать с учетной записью.

    	Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Убедитесь, что в вашей подписке зарегистрированы поставщики Azure для служб восстановления и службы Site Recovery, выполнив следующие команды:

	- `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
	-  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	Если в выходных данных этих двух команд для параметра RegistrationState установлено значение Registered, можно переходить к шагу 2. В противном случае необходимо зарегистрировать отсутствующего поставщика в своей подписке.


	Чтобы зарегистрировать поставщика Azure для Site Recovery, выполните следующую команду:

    	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
	
	Аналогичным образом, если вы используете командлеты служб восстановления в подписке впервые, требуется зарегистрировать поставщика Azure для служб восстановления. Но сначала для подписки необходимо разрешить доступ к поставщику служб восстановления, выполнив следующую команду:

		Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

	>[AZURE.TIP] Чтобы разрешить доступ к поставщику служб восстановления для подписки после успешного выполнения этой команды, может потребоваться около часа. Попытки зарегистрировать поставщика служб восстановления для подписки с использованием команды `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices` раньше могут завершиться ошибкой. Если это произойдет, подождите час и повторите попытку.

	Как только доступ к поставщику служб восстановления будет разрешен для подписки, зарегистрируйте этого поставщика с помощью следующей команды:

		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

	Убедитесь, что поставщики успешно зарегистрированы, выполнив команды `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` и `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

	

## Шаг 2. Настройка хранилища служб восстановления

1. Создайте группу ресурсов ARM для создания хранилища или воспользуйтесь существующей группой ресурсов. Чтобы создать группу ресурсов ARM, нужно выполнить следующую команду:

		New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

	Здесь переменная $ResourceGroupName содержит имя создаваемой группы ресурсов ARM, а $Geo — регион Azure, в котором ее необходимо создать (например, Южная Бразилия).

	Получить список групп ресурсов ARM в подписке можно с помощью командлета `Get-AzureRmResourceGroup`.

2. Создайте хранилище служб восстановления Azure следующим образом:

		$vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

	Получить список существующих хранилищ можно с помощью командлета `Get-AzureRmRecoveryServicesVault`.

> [AZURE.NOTE] Если требуется выполнять операции с хранилищами ASR, созданными с помощью классического портала или модуля PowerShell управления службой Azure, список таких хранилищ можно получить с помощью командлета `Get-AzureRmSiteRecoveryVault`. Для каждой новой операции рекомендуется создавать отдельное хранилище служб восстановления. Хранилища Site Recovery, созданные ранее, будут по-прежнему поддерживаться, но в них не будет новых функций.

## Шаг 3. Создание ключа регистрации хранилища

1. Создайте и скачайте ключ регистрации хранилища для хранилища.

    	Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -Path $path
2. Импортируйте скачанный файл параметров хранилища, чтобы задать контекст хранилища.
 
		Import-AzureRmSiteRecoveryVaultSettingsFile -Path $path 

## Шаг 4. Создание сайта Hyper-V и ключа регистрации хранилища для сайта

1. Создайте сайт Hyper-V следующим образом:
		
		$sitename = "MySite"                #Specify site friendly name
		New-AzureRmSiteRecoverySite -Name $sitename

	Этот командлет запускает задание ASR для создания сайта и возвращает объект этого задания. Дождитесь завершения задания и убедитесь, что оно выполнено успешно.

	Вы можете получить объект задания ASR и тем самым проверить текущее состояние задания с помощью командлета Get-AzureRmSiteRecoveryJob. 
2. Создайте и скачайте ключ регистрации для сайта:

		$SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
	
	Скопируйте скачанный ключ на узел Hyper-V. Этот ключ вам потребуется, чтобы зарегистрировать узел Hyper-V на сайте.

	
## Шаг 5. Установка поставщика Azure Site Recovery и агента служб восстановления Azure на узле Hyper-V

1. Скачайте установщик последней версии поставщика [здесь](https://aka.ms/downloaddra).
2. Запустите установщик на узле Hyper-V. По завершении установки перейдите к шагу регистрации.
3. При появлении запроса укажите скачанный ключ регистрации сайта и завершите регистрацию узла Hyper-V на сайте.
4. Убедитесь, что узел Hyper-V зарегистрирован на сайте с помощью следующей команды:

		$server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname 

## Шаг 6. Создание политики репликации и связывание ее с контейнером защиты

1. Создайте политику репликации:

		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points
		$storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id 

		$PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

	Проверьте возвращенный результат задания, чтобы убедиться, что политика репликации успешно создана.

	>[AZURE.IMPORTANT] Указанная учетная запись хранения должна находиться в том же регионе Azure, что и хранилище служб восстановления. В этой учетной записи также необходимо включить функцию георепликации.
	>
	> - Если тип указанной учетной записи хранения Site Recovery — служба хранилища Azure (классический способ), отработка отказа защищенной машины будет выполнена с переносом в Azure IaaS (классический способ).
	> - Если тип указанной учетной записи хранения Site Recovery — служба хранилища Azure (с использованием диспетчера ресурсов Azure), отработка отказа защищенной машины будет выполнена с переносом в Azure IaaS (с использованием диспетчера ресурсов Azure).
	
 
2. Получите соответствующий сайту контейнер защиты:
		
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.	Свяжите контейнер защиты с политикой репликации:

		$Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

	Дождитесь завершения этого задания и убедитесь, что оно выполнено успешно.

##Шаг 7. Включение защиты для виртуальных машин

1. Получите объект защиты, соответствующий виртуальной машине, которую необходимо защитить:
		
		$VMFriendlyName = "Fabrikam-app"                    #Name of the VM 
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Включите защиту виртуальной машины:
		
		$Ostype = "Windows"                                 # "Windows" or "Linux"
		$DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

	>[AZURE.IMPORTANT] Указанная учетная запись хранения должна находиться в том же регионе Azure, что и хранилище служб восстановления. В этой учетной записи также необходимо включить функцию георепликации.
	>
	> - Если тип указанной учетной записи хранения Site Recovery — служба хранилища Azure (классический способ), отработка отказа защищенной машины будет выполнена с переносом в Azure IaaS (классический способ).
	> - Если тип указанной учетной записи хранения Site Recovery — служба хранилища Azure (с использованием диспетчера ресурсов Azure), отработка отказа защищенной машины будет выполнена с переносом в Azure IaaS (с использованием диспетчера ресурсов Azure).

	> Если к виртуальной машине, которую необходимо защитить, подключены несколько дисков, нужно указать диск операционной системы с помощью параметра OSDiskName.

3. Дождитесь, пока виртуальная машина перейдет в защищенное состояние после начальной репликации. Это займет некоторое время в зависимости от таких факторов, как объем реплицируемых данных и пропускная способность вышестоящих служб в Azure. Как только виртуальная машина перейдет в защищенное состояние, параметры задания State и StateDescription будут обновлены, как указано ниже.

		
		PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

		PS C:\> $DRjob | Select-Object -ExpandProperty State
		Succeeded

		PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
		Completed

4. Обновите свойства восстановления, такие как размер роли виртуальной машины и сеть Azure, чтобы подключить сетевую карту виртуальной машины после отработки отказа.

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



## Шаг 8. Запуск тестовой отработки отказа

1. Запустите задание тестовой отработки отказа:
		
		$nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

		$TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Убедитесь, что тестовая виртуальная машина создана в Azure. (После создания тестовой виртуальной машины в Azure задание тестовой отработки отказа будет приостановлено. Когда задание будет возобновлено, как показано на следующем шаге, будут очищены все созданные артефакты. После этого задание будет завершено.)

3. Завершите выполнение тестовой отработки отказа.

    	$TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

<!---HONumber=AcomDC_0323_2016-->