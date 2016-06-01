<properties
	pageTitle="Репликация виртуальных машин Hyper-V в облаках VMM с помощью Azure Site Recovery и PowerShell | Microsoft Azure"
	description="Узнайте, как автоматизировать репликацию виртуальных машин Hyper-V в облаках VMM с помощью Azure Site Recovery и PowerShell."
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="lauraa"/>

# Репликация виртуальных машин Hyper-V из облаков VMM в Azure с помощью PowerShell (классическая модель)

> [AZURE.SELECTOR]
- [Портал Azure](site-recovery-vmm-to-azure.md)
- [PowerShell в режиме ARM](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Классический портал](site-recovery-vmm-to-azure-classic.md)
- [PowerShell — классическая модель](site-recovery-deploy-with-powershell.md)


## Обзор

Служба Azure Site Recovery помогает реализовать стратегии непрерывности бизнеса и восстановления после сбоев (BCDR), управляя процессами репликации, отработки отказа и восстановления виртуальных машин в ряде сценариев развертывания. Полный список сценариев развертывания приведен в статье [Обзор Site Recovery](site-recovery-overview.md).

В этой статье показано, как использовать PowerShell для автоматизации распространенных задач, которые необходимо выполнять при настройке Azure Site Recovery для репликации виртуальных машин Hyper-V в облаках VMM System Center в службу хранилища Azure.

Статья включает перечень необходимых для сценария компонентов и инструкции по настройке хранилища Site Recovery, установке Провайдера Azure Site Recovery на сервере VMM источника, регистрации сервера в хранилище, добавлению учетной записи хранения Azure, установке агента Azure Recovery Services на серверах узлов Hyper-V, настройке параметров защиты для облаков VMM, которые будут применяться ко всем защищенным виртуальным машинам, и последующему включению защиты для этих виртуальных машин. В качестве завершения выполните отработку отказа, чтобы убедиться в том, что все работает правильно.

Если у вас возникают проблемы, опубликуйте свои вопросы на [форуме по Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания.



## Перед началом работы

Убедитесь, что выполнены следующие предварительные требования.

### Предварительные требования Azure

- Вам потребуется учетная запись [Microsoft Azure](https://azure.microsoft.com/). Начните с [бесплатной пробной версии](pricing/free-trial/).
- Для хранения реплицируемых данных потребуется учетная запись хранения Azure. На учетной записи необходимо включить георепликацию. Она должна находиться в том же регионе, что и хранилище Azure Site Recovery, и быть связана с той же подпиской. См. [дополнительные сведения о службе хранилища Azure](../storage/storage-introduction.md).
- Вам потребуется убедиться, что защищаемые виртуальные машины соответствуют [предварительным требованиям к виртуальным машинам Azure](site-recovery-best-practices.md#virtual-machines).

### Предварительные требования VMM
- Вам потребуется сервер VMM под управлением System Center 2012 R2.
- Требуется по крайней мере одно облако на сервере VMM, для которого предполагается настроить защиту. Облако должно содержать:
	- одну или несколько групп узлов VMM;
	- один или несколько серверов узлов или кластеров Hyper-V в каждой группе узлов;
	- одну или несколько виртуальных машин на исходном сервере Hyper-V.

### Предварительные требования Hyper-V

- Серверы узлов Hyper-V должны работать под управлением Windows Server 2012 (или более поздних версий) с ролью Hyper-V и установленными последними обновлениями.
- Если Hyper-V выполняется в кластере, учтите, что брокер кластера не создается автоматически при использовании кластера на основе статических IP-адресов. Вам потребуется настроить брокер кластера вручную. Для этого выберите «Диспетчер сервера» > «Диспетчер отказоустойчивости кластеров», подключитесь к кластеру, щелкните **Настройка роли** и выберите **Брокер реплики Hyper-V** в окне мастера высокой доступности **Выберите роль**. 
- Все кластеры или серверы узлов Hyper-V, для которых вы настраиваете управление защитой, должны быть включены в облако VMM.

### Предварительные требования сетевого сопоставления
В рамках защиты виртуальных машин в Azure с помощью сопоставления сетей осуществляется сопоставление сетей виртуальных машин на исходном сервере VMM и целевых сетей Azure, что обеспечивает следующие преимущества.

- Все компьютеры, перешедшие в состояние отказа в одной и той же сети, могут подключиться друг к другу, независимо от назначенного им плана восстановления.
- При наличии сетевого шлюза в целевой сети Azure виртуальные машины могут подключиться к другим локальным виртуальным машинам.
- Если сопоставление не включено, то после отработки отказа в Azure могут подключаться друг к другу только виртуальные машины, переключающиеся на другой ресурс в рамках одного плана восстановления.

Для развертывания сетевого сопоставления требуется следующее.

- Защищаемые виртуальные машины на исходном сервере VMM должны быть подключены к сети виртуальных машин. Эта сеть должна быть подключена к логической сети, которая связана с облаком.
- Сеть Azure, к которой могут подключаться реплицированные виртуальные машины после отработки отказа. Эту сеть вы выбираете во время отработки отказа. Сеть должна быть в том же регионе, в котором действует ваша подписка Azure Site Recovery.
- [Узнайте больше](site-recovery-network-mapping.md) о сетевом сопоставлении.

###Необходимые компоненты PowerShell
Перед началом работы убедитесь, что среда Azure PowerShell готова к работе. Если вы уже используете PowerShell, необходимо выполнить обновление до версии 0.8.10 или более поздней версии. Дополнительные сведения о настройке Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md). После установки и настройки PowerShell все доступные командлеты для службы можно просмотреть [здесь](https://msdn.microsoft.com/library/dn850420.aspx).

Дополнительные сведения об использовании командлетов, например о типичной обработке значений параметров, входных и выходных параметров в Azure PowerShell, приведены в статье [Начало работы с командлетами Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Шаг 1. Настройка подписки 

В PowerShell выполните следующие командлеты.

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Замените элементы в «< >» соответствующей информацией для своей системы.

## Шаг 2. Создание хранилища Site Recovery

В PowerShell замените элементы в «< >» соответствующей информацией для своей системы и выполните указанные ниже команды.

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## Шаг 3. Создание ключа регистрации хранилища

Создайте ключ регистрации в хранилище. После загрузки поставщика Azure Site Recovery и установки его на сервер VMM этот ключ используется для регистрации сервера VMM в хранилище.

1.	Получите файл настроек хранилища и задайте контекст:
	
	```
	
	$VaultName = "<testvault123>"
	$VaultGeo  = "<Southeast Asia>"
	$OutputPathForSettingsFile = "<c:>"
	
	$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	Задайте контекст хранилища, выполнив следующие команды:
	
	```
	
	$VaultSettingFilePath = $vaultSetingsFile.FilePath 
	$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
	
	```

## Шаг 4. Установка поставщика Azure Site Recovery

1.	На компьютере с VMM создайте каталог, выполнив следующую команду:
	
	```
	
	pushd C:\ASR\
	
	```
	
2.	Распакуйте файлы с помощью загруженного поставщика, выполнив следующую команду:
	
	```
	
	AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3.	Используйте следующую команду для установки провайдера:
	
	```
	
	.\SetupDr.exe /i
	
	```
	
	```
	
	$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
		$isNotInstalled = $true;
		if(Test-Path $installationRegPath)
		{
			$isNotInstalled = $false;
		}
	}While($isNotInstalled)
	
	```
	
	Дождитесь завершения процесса установки.
	
4.	Зарегистрируйте сервер в хранилище, используя следующую команду:
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## Шаг 5. Создание учетной записи хранения Azure

Если у вас нет учетной записи хранилища Azure, создайте учетную запись с включенной георепликацией, выполнив следующую команду:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Обратите внимание, что учетная запись хранилища должна находиться в том же регионе, что и служба Azure Site Recovery, и должна быть связана с той же подпиской.


## Шаг 6. Установка агента служб восстановления Azure

С помощью портала Azure установите агент служб восстановления Azure на всех серверах узлов Hyper-V в облаках VMM, для которых предполагается настроить защиту.

Выполните следующую команду на всех узлах VMM:

```

marsagentinstaller.exe /q /nu

```


## Шаг 7. Настройка параметров защиты облачных систем

1.	Создайте профиль защиты облака в Azure, выполнив следующую команду:
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	Получите контейнер защиты, выполнив следующие команды:
	
	```
	
	$PrimaryCloud = "testcloud"
	$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	Свяжите контейнер защиты с облаком:
	
	```
	
	$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	После завершения задания выполните следующую команду:

	
		$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
		if($job -eq $null -or $job.StateDescription -ne "Completed")
		{
		$isJobLeftForProcessing = $true;
		}


5.	После завершения обработки задания выполните следующую команду:

	
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
		
	

Для проверки выполнения операции выполните действия, описанные в разделе [Мониторинг активности](#monitor).

## Шаг 8. Настройка сетевого сопоставления
Прежде чем приступать к настройке сетевого сопоставления, убедитесь, что виртуальные машины на исходном сервере VMM подключены к сети виртуальных машин. Кроме того, создайте одну или несколько виртуальных сетей Azure. Учтите, что несколько сетей виртуальных машин можно сопоставить с одной сетью Azure.

Учтите, что, если целевая сеть включает несколько подсетей и одна из этих подсетей имеет то же имя, что и подсеть, в которой размещается исходная виртуальная машина, реплика виртуальной машины будет подключена к этой целевой подсети после отработки отказа. При отсутствии целевой подсети с соответствующим именем виртуальная машина будет подключена к первой подсети в сети.

Первая команда возвращает серверы для текущего хранилища Azure Site Recovery. Команда сохраняет серверы Microsoft Azure Site Recovery в массиве $Servers.

	$Servers = Get-AzureSiteRecoveryServer


Вторая команда получает сеть восстановления сайта для первого сервера в массиве $Servers. Команда сохраняет сети в переменной $Networks.


	$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

Третья команда получает ваши подписки Azure с помощью командлета Get-AzureSubscription и затем сохраняет их в переменной $Subscriptions.

	$Subscriptions = Get-AzureSubscription



Четвертая команда получает виртуальные сети Azure с помощью командлета Get-AzureVNetSite и затем сохраняет это значение в переменной $AzureVmNetworks.


	$AzureVmNetworks = Get-AzureVNetSite



Последний командлет создает сопоставление между основной сетью и сетью виртуальной машины Azure. Командлет устанавливает основную сеть в качестве первого элемента $Networks. Командлет устанавливает сеть виртуальной машины в качестве первого элемента $AzureVmNetworks по идентификатору элемента. Команда содержит идентификатор вашей подписки Azure.


	New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## Шаг 9. Включение защиты для виртуальных машин

После успешной настройки серверов, облаков и сетей можно включить защиту для виртуальных машин в облаке. Обратите внимание на следующее.

Виртуальные машины должны соответствовать [предварительным требованиям к виртуальным машинам Azure](site-recovery-best-practices.md#virtual-machines).

Для обеспечения защиты должны быть заданы соответствующие свойства операционной системы и диска с операционной системой. Данные свойства можно установить при создании виртуальной машины в VMM с помощью шаблона виртуальной машины. Эти свойства также можно задать для существующих виртуальных машин на вкладках **Общие** и **Конфигурация оборудования** свойств виртуальной машины. Если установка этих свойств в VMM не была выполнена, сделать это можно на портале Azure Site Recovery.


	
1.	Чтобы включить защиту, выполните следующую команду для получения контейнера защиты:
		
		$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	

	
2. Получите объект защиты (виртуальную машину), выполнив следующую команду:
	
	
		$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
	
		
3. Включите аварийное восстановление для виртуальной машины, выполнив следующую команду:


	
		$jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity 	-Protection Enable -Force
	

	
## Выполните тестирование развертывания

Чтобы протестировать развертывание, можно запустить тестовую отработку отказа для отдельной виртуальной машины или создать план восстановления, состоящий из нескольких виртуальных машин, и запустить тестовую отработку отказа плана. Тестовая обработка отказа имитирует механизм отработки отказа и восстановления в изолированной сети. Обратите внимание на следующее.

- При необходимости подключиться к виртуальной машине в Azure с использованием удаленного рабочего стола после отработки отказа, включите подключение удаленного рабочего стола на виртуальной машине до запуска тестирования отработки отказа.
- После отработки отказа для подключения к виртуальной машине в Azure с удаленного рабочего стола воспользуйтесь публичным IP-адресом. Если необходимо это сделать, убедитесь в отсутствии политик домена, которые не допустили бы подключение к виртуальной машине с использованием общедоступного адреса.

Для проверки выполнения операции выполните действия, описанные в разделе [Мониторинг активности](#monitor).

### Создайте план восстановления

1. Создайте XML-файл в качестве шаблона для плана восстановления, используя приведенные ниже данные, и сохраните его под именем «C:\\RPTemplatePath.xml».
2. Измените поля nodeId, Name, PrimaryServerId и SecondaryServerId объекта RecoveryPlan.
3. Измените поле PrimaryProtectionEntityId объекта ProtectionEntity (указав в нем значение vmid из VMM).
4. Можно добавить дополнительные виртуальные машины, добавив дополнительные узлы ProtectionEntity.
	
	
	
		<#
		<?xml version="1.0" encoding="utf-16"?>
		<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
		  <Actions />
		  <ActionGroups>
		    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
		      <PreActionSequence />
		      <PostActionSequence />
		    </ShutdownAllActionGroup>
		    <FailoverAllActionGroup Id="FailoverAllActionGroup">
		      <PreActionSequence />
		      <PostActionSequence />
		    </FailoverAllActionGroup>
		    <BootActionGroup Id="DefaultActionGroup">
		      <PreActionSequence />
		      <PostActionSequence />
		      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
		    </BootActionGroup>
		  </ActionGroups>
		  <ActionGroupSequence>
		    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
		    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
		    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
		  </ActionGroupSequence>
		</RecoveryPlan>
		#>
	

	
4. Заполните данные шаблона:
	
		
		$TemplatePath = "C:\RPTemplatePath.xml";
	

	
5. Создайте план восстановления:

		$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;
	
	
	
### Запуск тестовой отработки отказа

1.	Получите объект RecoveryPlan, выполнив следующую команду:

		$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;

	
2.	Запустите тестовую обработку отказа с помощью следующей команды:
	
	
		$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
		
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

[Узнайте больше](https://msdn.microsoft.com/library/dn850420.aspx) о командлетах PowerShell для Azure Site Recovery. </a>.

<!---HONumber=AcomDC_0518_2016-->