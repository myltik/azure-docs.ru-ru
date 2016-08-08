<properties
	pageTitle="Репликация виртуальных машин Hyper-V из облаков VMM на вторичный сайт VMM с помощью PowerShell (Resource Manager) | Microsoft Azure"
	description="Описывается, как развернуть Azure Site Recovery, чтобы управлять репликацией, отработкой отказа и восстановлением виртуальных машин Hyper-V в облаках VMM на вторичный сайт VMM с помощью PowerShell (Resource Manager)."
	services="site-recovery"
	documentationCenter=""
	authors="sujaytalasila"
	manager="rochakm"
	editor="raynew"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="sutalasi"/>

# Репликация виртуальных машин Hyper-V из облаков VMM на вторичный сайт VMM с помощью PowerShell (Resource Manager)

> [AZURE.SELECTOR]
- [Портал Azure](site-recovery-vmm-to-vmm.md)
- [Классический портал](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell — Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Вас приветствует служба Azure Site Recovery! Эта статья поможет вам выполнить репликацию локальных виртуальных машин Hyper-V, управляемых в облаках System Center Virtual Machine Manager (VMM), на вторичный сайт.

В этой статье показано, как использовать PowerShell для автоматизации распространенных задач, которые необходимо выполнять при настройке Azure Site Recovery для репликации виртуальных машин Hyper-V из облаков VMM System Center в облака VMM System Center на вторичном сайте.

В этой статье рассматриваются необходимые условия для реализации сценария, а также описываются следующие действия.

- Настройка хранилища служб восстановления.
- Установка на исходном и целевом серверах VMM поставщика Azure Site Recovery.
- Регистрация серверов VMM в хранилище.
- Настройка политики репликации для облака VMM. Параметры репликации в политике будут применены ко всем защищенным виртуальным машинам.
- Включение защиты виртуальных машин.
- Тестирование отработки отказа виртуальных машин по отдельности или по плану восстановления, позволяющее убедиться в том, что все работает ожидаемым образом.
- Выполнение плановой или внеплановой отработки отказа виртуальных машин по отдельности или по плану восстановления, позволяющее убедиться в том, что все работает ожидаемым образом.

Если у вас возникают проблемы, опубликуйте свои вопросы на [форуме по Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../resource-manager-deployment-model.md). Кроме того, Azure предоставляет два портала — классический портал Azure, поддерживающий классическую модель развертывания, и портал Azure, поддерживающий обе модели развертывания. В этой статье описывается модель развертывания с использованием менеджера ресурсов.



## Предварительные требования для локальной среды

Вот что потребуется на первичных и вторичных локальных сайтах для развертывания этого сценария.

**Предварительные требования** | **Дополнительные сведения** 
--- | ---
**VMM** | Рекомендуется развернуть по серверу VMM на первичном и вторичном сайтах.<br/><br/> Вы можете также [выполнять репликацию между облаками на одном сервере VMM](site-recovery-single-vmm.md). Для этого вам потребуется, чтобы на сервере VMM было настроено по крайней мере два облака.<br/><br/> На серверах VMM должен выполняться как минимум System Center 2012 с пакетом обновления 1 (SP1) с последними обновлениями.<br/><br/> Для каждого сервера VMM должно быть настроено одно или несколько облаков, и для каждого облака должен быть задан профиль загрузки Hyper-V. <br/><br/>Облака должны содержать одну или несколько групп узлов VMM. <br/><br/>Дополнительные сведения о настройке облаков VMM см. в разделах [Настройка структуры облака VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) и [Пошаговое руководство. Создание частных облаков с помощью VMM в System Center 2012 с пакетом обновления 1](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Серверы VMM должны иметь доступ к Интернету. 
**Hyper-V** | Серверы Hyper-V должны работать под управлением Windows Server 2012 (или более поздних версий) с ролью Hyper-V и установленными последними обновлениями.<br/><br/> Сервер Hyper-V должен содержать одну или несколько виртуальных машин.<br/><br/> Серверы узлов Hyper-V должны быть размещены в первичном и вторичном облаках VMM.<br/><br/> Если Hyper-V выполняется в кластере на основе Windows Server 2012 R2, то необходимо установить [обновление 2961977](https://support.microsoft.com/kb/2961977)<br/><br/>. Если Hyper-V выполняется в кластере на основе Windows Server 2012, то имейте ввиду, что брокер кластера не создается автоматически, если в кластере используются статические IP-адреса. Вам потребуется настроить брокер кластера вручную. [Подробная информация](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Поставщик** | Во время развертывания службы Site Recovery установите на серверы VMM поставщик Azure Site Recovery. Поставщик обменивается данными со службой Site Recovery через порт HTTPS 443 для управления репликацией. Репликация данных осуществляется между сервером-источником и сервером-приемником Hyper-V через локальную сеть или VPN-подключение.<br/><br/> Поставщику, запущенному на сервере VMM, нужен доступ к следующим URL-адресам: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net.<br/><br/> Кроме того, разрешите в брандмауэре обмен данными между серверами VMM и [диапазонами IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653), а также разрешите использовать протокол HTTPS (443).

### Предварительные требования сетевого сопоставления
При сетевом сопоставлении сопоставляются сети виртуальных машин VMM первичного и вторичного серверов VMM. Вот для чего это делается:

- Оптимальное размещение виртуальных машин реплик на вторичных узлах Hyper-V после отработки отказа.
- Подключение виртуальных машин реплик к соответствующим сетям виртуальных машин.
- Если не настроить сетевое сопоставление, виртуальные машины реплик не будут подключены к каким-либо сетям после отработки отказа.
- Вот что понадобится, если вы хотите настроить сетевое сопоставление во время развертывания службы Site Recovery.

	- Виртуальные машины на сервере узла Hyper-V должны быть подключены к сети виртуальных машин VMM. Эта сеть должна быть подключена к логической сети, которая связана с облаком.
	- Проверьте, настроена соответствующая сеть виртуальной машины для вторичного облака, которое будет использоваться для восстановления. Эта сеть виртуальной машины должна быть связана с логической сетью, которая связана с вторичным облаком.


Узнайте больше о настройке сетей VMM в следующих статьях:

- [Настройка логических сетей в VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Настройка сетей виртуальных машин и шлюзов в VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[Узнайте больше](site-recovery-network-mapping.md) о принципе действия сетевого сопоставления.

###Необходимые компоненты PowerShell
Перед началом работы убедитесь, что среда Azure PowerShell готова к работе. Если вы уже используете PowerShell, необходимо выполнить обновление до версии 0.8.10 или более поздней версии. Дополнительные сведения о настройке PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). После установки и настройки PowerShell все доступные командлеты для службы можно просмотреть [здесь](https://msdn.microsoft.com/library/dn850420.aspx).

Дополнительные сведения об использовании командлетов, например о типовой обработке значений параметров, входных и выходных параметров в Azure PowerShell, приведены в статье [Начало работы с командлетами Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Шаг 1. Настройка подписки 

1. В Azure Powershell войдите в свою учетную запись Azure с помощью следующих командлетов:
 
		$UserName = "<user@live.com>"
		$Password = "<password>"
		$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
		$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
		Login-AzureRmAccount #-Credential $Cred 
	

2. Получите список своих подписок. При этом также будет выведен список идентификаторов subscriptionID для каждой подписки. Запишите идентификатор subscriptionID подписки, в которой хотите создать хранилище служб восстановления.

		Get-AzureRmSubscription 

3. Задайте подписку, в которой будет создано хранилище служб восстановления, указав идентификатор подписки.

		Set-AzureRmContext –SubscriptionID <subscriptionId>


## Шаг 2. Создание хранилища служб восстановления 

1. Если у вас еще нет группы ресурсов Azure Resource Manager, создайте ее.

		New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Создайте новое хранилище служб восстановления и сохраните созданный объект хранилища ASR в переменной, которая будет использоваться далее. Объект хранилища ASR также можно получить после создания с помощью командлета Get-AzureRMRecoveryServicesVault:-

		$vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## Шаг 3. Настройка контекста для хранилища служб восстановления

1.  Если хранилище уже создано, выполните следующую команду, чтобы получить его.

		$vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  Задайте контекст хранилища, выполнив указанную ниже команду.

		Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault



## Шаг 4. Установка поставщика Azure Site Recovery

1.	На компьютере с VMM создайте каталог, выполнив следующую команду:
	
		New-Item c:\ASR -type directory
		
2.	Распакуйте файлы с помощью загруженного поставщика, выполнив следующую команду:
	
		pushd C:\ASR\
		.\AzureSiteRecoveryProvider.exe /x:. /q

	
3.	Используйте следующую команду для установки провайдера:
	
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
	
4.	Зарегистрируйте сервер в хранилище, используя следующую команду:
	
		$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
		pushd $BinPath
		$encryptionFilePath = "C:\temp".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## Шаг 5. Создание и связывание политики репликации

1.	Создайте политику репликации Hyper-V 2012 R2, выполнив следующую команду.

	
		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$RepProvider = HyperVReplica2012R2
		$Recoverypoints = 24            		#specify the number of hours to retain recovery pints
		$AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
		$AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
		$AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
		$InitialRepMethod = "Online" #options are "Online" or "Offline"

		$policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

	> [AZURE.NOTE] Облако VMM может содержать узлы Hyper-V под управлением разных версий Windows Server (как уже упоминалось в предварительных требованиях к Hyper-V), но политика репликации зависит от версии ОС. Если вы используете узлы с разными версиями операционных систем, то создайте отдельные политики репликации для каждого типа версии ОС. Пример. Если у вас есть пять узлов с Windows Server 2012 и три с Windows Server 2012 R2, то создайте две политики репликации — по одной для каждого типа версии ОС.

2.	Получите первичный контейнер защиты (основного облака VMM) и контейнер защиты для восстановления (облако VMM восстановления), выполнив следующие команды.
	
		$PrimaryCloud = "testprimarycloud"
		$primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

		$RecoveryCloud = "testrecoverycloud"
		$recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.	Получите политику, созданную на шаге 1, указав ее понятное имя.

		$policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.	Запустите связывание контейнера защиты (облака VMM) с политикой репликации.

		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.	Дождитесь завершения задания связывания политики. Проверить, завершено ли это задание, можно с помощью следующего фрагмента кода PowerShell.
   
		$job = Get-AzureRmSiteRecoveryJob -Job $associationJob
   		if($job -eq $null -or $job.StateDescription -ne "Completed")
   		 {
        	$isJobLeftForProcessing = $true;
    	}

 	После завершения обработки задания выполните следующую команду:

		if($isJobLeftForProcessing)
    	{
    	Start-Sleep -Seconds 60
    	}
        }While($isJobLeftForProcessing)


Для проверки выполнения операции выполните действия, описанные в разделе [Мониторинг активности](#monitor).

## Действие 5. Настройка сетевого сопоставления

1. Первая команда возвращает серверы для текущего хранилища Azure Site Recovery. Команда сохраняет серверы Microsoft Azure Site Recovery в массиве $Servers.

		$Servers = Get-AzureRmSiteRecoveryServer

2. Приведенные ниже команды получают сеть Site Recovery для исходного и целевого серверов VMM.

    	$PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

		$RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

	
	> [AZURE.NOTE] Исходный сервер VMM может быть первым или вторым в массиве серверов. Проверьте имена серверов VMM и получите соответствующие сети.


4. Последний командлет создает сопоставление между основной сетью и сетью восстановления. Этот командлет указывает основную сеть в качестве первого элемента $PrimaryNetworks, а сеть восстановления — в качестве первого элемента $RecoveryNetworks.

		New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## Шаг 6. Включение защиты для виртуальных машин

После успешной настройки серверов, облаков и сетей можно включить защиту для виртуальных машин в облаке.


  1. Чтобы включить защиту, выполните следующую команду для получения контейнера защиты:
	
			$PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
	
  2. Получите объект защиты (виртуальную машину), выполнив следующую команду:
	
	 		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
		
  3. Включите репликацию для виртуальной машины, выполнив следующую команду.

			$jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## Выполните тестирование развертывания

Чтобы протестировать развертывание, можно запустить тестовую отработку отказа для отдельной виртуальной машины или создать план восстановления, состоящий из нескольких виртуальных машин, и запустить тестовую отработку отказа плана. Тестовая обработка отказа имитирует механизм отработки отказа и восстановления в изолированной сети.

> [AZURE.NOTE] Можно создать план восстановления для приложения на портале Azure.

Для проверки выполнения операции выполните действия, описанные в разделе [Мониторинг активности](#monitor).


### Запуск тестовой отработки отказа

1.	Выполните приведенные ниже командлеты, чтобы получить сеть виртуальных машин, отработку отказа виртуальных машин в которую вы хотите проверить.

		$Servers = Get-AzureRmSiteRecoveryServer
		$RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.	Выполните тестовую отработку отказа виртуальной машины следующим образом.
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.	Выполните тестовую отработку отказа по плану восстановления следующим образом.
	
		$recoveryplanname = "test-recovery-plan"

		$recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

		$jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### Запуск запланированной отработки отказа

1. Выполните плановую отработку отказа виртуальной машины следующим образом.
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Выполните плановую отработку отказа по плану восстановления следующим образом.
	
		$recoveryplanname = "test-recovery-plan"

		$recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

		$jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### Запуск незапланированной отработки отказа

1. 1\. Выполните внеплановую отработку отказа виртуальной машины следующим образом.
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2\. Выполните внеплановую отработку отказа по плану восстановления следующим образом.
		
		$recoveryplanname = "test-recovery-plan"

		$recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

		$jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
	
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



## Дальнейшие действия

[Узнайте больше](https://msdn.microsoft.com/library/azure/mt637930.aspx) об использовании командлетов PowerShell инструмента Azure Resource Manager для службы Azure Site Recovery.

<!---HONumber=AcomDC_0727_2016-->