<properties 
	pageTitle="Аварийное восстановление с помощью SQL Server и Azure Site Recovery" 
	description="Azure Site Recovery координирует репликацию, отработку отказа и восстановление SQL Server на дополнительный локальный сайт или в Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="raynew"/>


# Аварийное восстановление с помощью SQL Server и Azure Site Recovery 

Служба Azure Site Recovery помогает реализовать стратегии непрерывности бизнеса и восстановления после сбоев (BCDR), управляя процессами репликации, отработки отказа и восстановления виртуальных машин и физических серверов. Site Recovery поддерживает ряд механизмов репликации для обеспечения постоянной защиты, репликации и отработку отказа машин в Azure или в дополнительный центр обработки данных. Обзор всех сценариев развертывания см. в статье [Обзор Azure Site Recovery](site-recovery-overview.md).

 В этой статье описывается способ организации защиты серверной части SQL Server приложения с помощью сочетания технологий SQL Server BCDR и Site Recovery. Для развертывания сценариев, описанных в этой статье, требуется хорошее понимание функций SQL Server BCDR (отказоустойчивая кластеризация, группы доступности AlwaysOn, зеркальное отображение базы данных, доставка журналов) и Site Recovery.



## Обзор

В основе многих рабочих нагрузок используется SQL Server. Для реализации служб данных приложения, такие как SharePoint, Dynamics и SAP, используют SQL Server. Для защиты и восстановления баз данных SQL Server используются такие компоненты SQL Server для обеспечения высокой доступности и аварийного восстановления, как группы доступности SQL Server.

Site Recovery позволяет защитить сервер SQL Server, выступающий в качестве виртуальной машины Hyper-V, виртуальной машины VMware или физического сервера.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td colspan = "2"><b>Hyper-V</b></td>
		<td colspan = "2"><b>VMware</b></td>
		<td colspan = "2"><b>Физический сервер</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Из локальной среды в локальную среду</td>
		<td>Из локальной среды в Azure</td>
		<td>Из локальной среды в локальную среду</td>
		<td>Из локальной среды в Azure</td>
		<td>Из локальной среды в локальную среду</td>
		<td>Из локальной среды в Azure</td>
    </tr>
	<tr align="left" valign="top">
		<td>Да</td>
		<td>Да</td>
		<td>Да</td>
		<td>Скоро</td>
		<td>Да</td>
		<td>Скоро</td>
    </tr>
    </tbody>
    </table>

## Поддержка и интеграция

Site Recovery может интегрироваться с собственными технологиями SQL Server BCDR, перечисленными в таблице ниже, для создания решения аварийного восстановления.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Компонент</b></td>
		<td><b>Дополнительные сведения</b></td>
		<td><b>Версия SQL Server</b></td>
    </tr>
    </tr><tr align="left" valign="top">
		<td><b>Группы доступности AlwaysOn</b></td>
		<td><p>Несколько отдельных экземпляров SQL Server, каждый из которых запущен в отказоустойчивом кластере с несколькими узлами.</p> <p>Базы данных можно объединить в группы отработки отказа, которые затем можно скопировать (зеркальное отображение) на экземпляры SQL Server, благодаря чему исключается необходимость в общем хранилище.</p> <p>Обеспечивает аварийное восстановление между основным сайтом и одним или несколькими дополнительными сайтами. Два узла можно настроить в кластере без общих ресурсов, в котором базы данных SQL Server настроены в группе доступности с синхронной репликацией и автоматической отработкой отказа.</p></td>
		<td>SQL Server 2014/2012 Enterprise</td>
    </tr>
	<tr align="left" valign="top">
		<td><b>Отказоустойчивая кластеризация (AlwaysOn FCI)</b></td>
		<td><p>SQL Server применяет отказоустойчивую кластеризацию Windows для обеспечения высокого уровня доступности локальных рабочих нагрузок SQL Server.</p><p>Узлы, на которых выполняются экземпляры SQL Server с общими дисками, настроены в отказоустойчивом кластере. Если экземпляр выходит из строя, выполняется переход кластера на другой ресурс.</p> <p>Кластер не обеспечивает защиту от сбоев или простоев в общем хранилище. Общий диск может быть реализован с использованием интерфейса iSCSI, Fiber Channel или с помощью в общих VHDX-файлов.</p></td>
		<td><p>Выпуски SQL Server Enterprise</p> <p>SQL Server Standard (ограничено только двумя узлами)</p></td>
	<tr align="left" valign="top">
		<td><b>Зеркальное отображение базы данных в режиме высокого уровня безопасности</b></td>
		<td>Защищает одну базу данных переносом в одну дополнительную копию. Доступно как в режиме репликации с высоким уровнем безопасности (синхронный режим), так и в режиме репликации с высоким уровнем производительности (асинхронный режим). Не требует наличия отказоустойчивого кластера.</td>
		<td><p>SQL Server 2008 R2</p><p>Все выпуски SQL Server Enterprise</p></td>
    </tr>
    </tr>
	<tr align="left" valign="top">
		<td><b>Автономный SQL Server</b></td>
		<td>SQL Server и база данных находятся на одном сервере (физическом или виртуальном). Если сервер виртуальный, то кластеризация размещения используется для обеспечения высокой доступности. Без высокой доступности гостевого уровня.</td>
		<td>Выпуск Enterprise или Standard</td>
 
    </tbody>
    </table>

В таблице ниже перечислены рекомендации корпорации Майкрософт относительно интеграции технологий SQL Server BCDR в развертывание Site Recovery.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Версия</b></td>
		<td><b>Выпуск</b></td>
		<td><b>Развертывание</b></td>
		<td><b>Из локальной среды в локальную среду</b></td>
		<td><b>Из локальной среды в Azure</b>&lt;/td
    </tr>
    <tr align="left" valign="top">
		<td rowspan = "3">SQL Server 2014 или 2012</td>
		<td rowspan = "2">Enterprise</td>
		<td>Экземпляр отказоустойчивого кластера</td>
		<td>Группы доступности AlwaysOn</td>
		<td>Группы доступности AlwaysOn</td>
    </tr>
	<tr align="left" valign="top">
		<td>Группы доступности AlwaysOn для обеспечения высокой доступности</td>
		<td>Группа доступности AlwaysOn</td>
		<td>Группа доступности AlwaysOn</td>
    </tr>
	<tr align="left" valign="top">
		<td>Стандарт</td>
		<td>Экземпляр отказоустойчивого кластера</td>
		<td>Репликация Site Recovery с локальным зеркалом</td>
		<td>Репликация Site Recovery с локальным зеркалом</td>
    </tr>
	<tr align="left" valign="top">
		<td>Enterprise или Standard</td>
		<td>Автономный</td>
		<td>Репликация Site Recovery с локальным зеркалом</td>
		<td>Репликация Site Recovery с локальным зеркалом</td>
    </tr>
	<tr align="left" valign="top">
		<td>SQL Server 2008 R2</td><td>Enterprise или Standard</td>
		<td>Автономный</td>
		<td>Репликация Site Recovery с локальным зеркалом</td>
		<td>Репликация Site Recovery с локальным зеркалом</td>
    </tr>
    </tbody>
    </table>


## Предварительные условия для развертывания

Ниже перечислены условия, которые необходимо выполнить перед началом работы.


- Локальное развертывание SQL Server с поддерживаемой версией SQL Server. Обычно также потребуется служба Active Directory для SQL Server.
- Необходимые условия для сценария, который требуется развернуть, представлены в статье, посвященной каждому развертыванию. Ссылки на них приведены в статье [Обзор Site Recovery](site-recovery-overview.md).
- Если вы хотите настроить восстановление в Azure, на виртуальных машинах SQL Server потребуется запустить средство [оценки готовности виртуальной машины Azure](http://www.microsoft.com/download/details.aspx?id=40898), чтобы убедиться в их совместимости с Azure и Site Recovery.

## Настройка защиты

Вам потребуется выполнить несколько действий:

- настроить Active Directory;
- настроить защиту для кластера SQL Server или автономного сервера.

### Настройка Active Directory

Для правильной работы SQL Server на дополнительном сайте восстановления потребуется настроить службу Active Directory. Существует несколько вариантов настройки:

- **для малых предприятий** — если имеется небольшое количество приложений и один контроллер домена для локального сайта и вам требуется выполнить отработку отказа всего сайта, то рекомендуем использовать репликацию Site Recovery для репликации контроллера домена в дополнительный центр обработки данных или в Azure;

- **для средних и крупных предприятий** — если имеется большое количество приложений, вы используете лесу Active Directory и вам требуется выполнить отработку отказа для каждого отдельного приложения или рабочей нагрузки, рекомендуется настроить дополнительный контроллер домена в дополнительном центре обработки данных или в Azure. Обратите внимание, что при использовании групп доступности AlwaysOn для восстановления удаленного сайта рекомендуется настроить другой дополнительный контроллер домена на дополнительном сайте или в Azure, который будет использоваться для восстановленного экземпляра SQL Server.

При выполнении инструкций в этом документе предполагается, что в дополнительном расположении доступен контроллер домена.

### Настройка защиты для автономного сервера SQL Server


В этой конфигурации для защиты компьютера SQL Server рекомендуется использовать репликацию Site Recovery. Конкретные шаги зависят от того, настроен ли SQL Server в качестве виртуальной машины и физического сервера, а также от необходимости реплицировать в Azure или в дополнительный локальный сайт. Инструкции для всех сценариев развертывания см. в статье [Обзор Site Recovery](site-recovery-overview.md).


### Настройка защиты для кластера SQL Server (2012 или 2014 Enterprise)

Если SQL Server использует группы доступности для обеспечения высокой доступности или экземпляр отказоустойчивого кластера, то на сайте восстановления так же рекомендуется использовать группы доступности. Обратите внимание, что данное руководство предназначено для приложений, не использующих распределенные транзакции.

##### Из локальной среды в локальную среду

1. [Настройте базы данных](https://msdn.microsoft.com/library/hh213078.aspx) для их добавления в группы доступности.
2. Создайте на дополнительном сайте новую виртуальную сеть.
3. Настройте VPN типа "сеть-сеть" между новой виртуальной сетью и основным сайтом.
4. Создайте виртуальную машину на сайте восстановления и установите на нее SQL Server.
5. Расширьте существующие группы доступности AlwaysOn для новой виртуальной машины SQL Server. Настройте этот экземпляр SQL Server в качестве копии асинхронной реплики.
6. Создайте прослушиватель группы доступности или обновите существующий прослушиватель, включив в него виртуальную машину асинхронной реплики.
7. Убедитесь в том, что ферма приложений настроена с помощью прослушивателя. Если настройка выполнена с использованием имени сервера базы данных, обновите его, чтобы использовать прослушиватель, чтобы вам не пришлось перенастраивать ее после отработки отказа.

#### Из локальной среды в Azure

При репликации в Azure настройка нескольких групп доступности может оказаться затруднительной, поскольку для каждой группы доступности требуется выделенный прослушиватель, а настройка каждого прослушивателя требует отдельной облачной службы. Рекомендуется настроить одну группу доступности, в которую включены все базы данных.

1. Создайте виртуальную сеть Azure.
2. Настройте VPN типа "сеть-сеть" между локальным сайтом и этой сетью.
3. Создайте в сети новую виртуальную машину SQL Server Azure и настройте ее в качестве асинхронной реплики группы доступности. Если после отработки отказа в Azure для уровня SQL Server требуется высокая доступность, настройте в Azure две копии асинхронной реплики.
4. Настройте реплику контроллера домена в виртуальной сети.
5. Убедитесь, что на виртуальной машине включены расширения для виртуальной машины. Это необходимо для передачи в план восстановления сценариев, относящихся к SQL Server.
6. Настройте прослушиватель SQL Server для группы доступности с помощью внутренней подсистемы балансировки нагрузки Azure.
7. Настройте уровень приложений на использование прослушивателя для доступа к уровню базы данных. Для приложений, использующих распределенные транзакции, рекомендуется использовать репликацию Site Recovery с SAN или репликацией VMWare типа "сеть-сеть".

### Настройка защиты для кластера SQL Server (Standard или 2008 R2)

Для кластера под управлением выпуска SQL Server Standard или SQL Server 2008 R2 рекомендуется использовать репликацию Site Recovery для защиты SQL Server.

#### Из локальной среды в локальную среду

- Для среды Hyper-V, если приложение использует распределенные транзакции, рекомендуется развернуть [репликацию Site Recovery с SAN](site-recovery-vmm-san.md).

- Для среды VMware рекомендуется развернуть защиту типа ["VMware–VMware"](site-recovery-vmware-to-vmware.md).

#### Из локальной среды в Azure

Site Recovery не поддерживает гостевой кластер при репликации в Azure. SQL Server также не предоставляет экономичное решение для аварийного восстановления для выпуска Standard. Рекомендуется защитить локальный кластер SQL Server в автономный сервер SQL Server и восстановить его в Azure.


1. Настройте дополнительный автономный экземпляр SQL Server в локальном сайте.
2. Настройте этот экземпляр в качестве зеркальной копии баз данных, которые требуется защитить. Настройте зеркальное отображение в режиме высокого уровня безопасности.
3.	Настройте Site Recovery на локальном сайте в соответствии со средой ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) или [VMware](site-recovery-vmware-to-azure.md)).
4.	Воспользуйтесь репликацией Site Recovery для репликации нового экземпляра SQL Server в Azure. Это зеркальная копия с высоким уровнем безопасности, поэтому она будет синхронизирована с основным кластером, однако в Azure она будет реплицирована с помощью репликации Site Recovery.

На рисунке ниже иллюстрируется процесс настройки.

![Стандартный кластер](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##Создание планов восстановления

С помощью планов восстановления можно группировать компьютеры, для которых отработку отказа следует выполнять совместно. Узнайте подробнее о [планах восстановления](site-recovery-create-recovery-plans.md) и [отработке отказа](site-recovery-failover.md) перед началом работы.


### Создание плана восстановления для кластеров SQL Server (SQL Server 2012/2014 Enterprise)

#### Настройка сценариев SQL Server для отработки отказа в Azure

В этом случае мы используем пользовательские сценарии и функции автоматизации Azure для планов восстановления, чтобы настроить отработку отказа группы доступности SQL Server на основе сценариев.

1.	Создайте локальный файл сценария для отработки отказа группы доступности. В этом примере сценария указывается путь к группе доступности в реплике Azure и выполняется отработка отказа в этот экземпляр реплики. Этот сценарий будет выполняться на реплике виртуальной машины SQL Server путем его передачи с помощью расширения пользовательского сценария.



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Передайте этот сценарий в большой двоичный объект в учетной записи хранения Azure. Используйте пример ниже:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Создайте модуль Runbook службы автоматизации Azure для запуска сценариев на реплике виртуальной машины SQL Server в Azure. Для этого используйте указанный ниже образец сценария. [Узнайте подробнее](site-recovery-runbook-automation.md) об использовании модулей Runbook службы автоматизации в планах восстановления.

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	При создании плана восстановления для приложения добавьте шаг "pre-Group 1 boot" из сценария, который запускает модуль Runbook службы автоматизации для отработки отказа групп доступности.

#### Настройка сценариев SQL Server для отработки отказа на дополнительный сайт.

1. Добавьте этот образец сценария в библиотеку VMM на основном и дополнительном сайтах.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. При создании плана восстановления для приложения добавьте шаг "pre-Group 1 boot" из сценария, который запускает сценарий для отработки отказа групп доступности.

### Создание плана восстановления для кластеров SQL Server (Standard)

#### Настройка сценариев SQL Server для отработки отказа в Azure

1.	Создайте локальный файл сценария для отработки отказа зеркала базы данных SQL Server. Воспользуйтесь указанным ниже образцом сценария.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Передайте этот сценарий в большой двоичный объект в учетной записи хранения Azure. Воспользуйтесь указанным ниже образцом сценария.

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Создайте модуль Runbook службы автоматизации Azure для запуска сценария на реплике виртуальной машины SQL Server в Azure. Для этого используйте указанный ниже образец сценария. [Узнайте подробнее](site-recovery-runbook-automation.md) об использовании модулей Runbook службы автоматизации в планах восстановления. Перед этим сначала убедитесь в том, что на виртуальной машине SQL Server, для которой выполнена отработка отказа, запущен агент виртуальной машины.

    	workflow SQLAvailabilityGroupFailover
		{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    	$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    	#Connect to Azure
    	$AzureAccount = Add-AzureAccount -Credential $Cred
    	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    	InLineScript
    	{
     	#Update the script with name of your storage account, key and blob name
     	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     	Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     	if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       		{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       		}
     	else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
		}



4. Добавьте указанные ниже действия в плане восстановления для отработки отказа на уровне SQL Server.

	- Для запланированной отработки отказа добавьте на основой сайт сценарий для выключения основного кластера после "Group shutdown".
	- Добавьте виртуальную машину с зеркалом базы данных SQL Server в план восстановления, предпочтительно в первую группу загрузки.
3.	С помощью указанного выше сценария для автоматизации добавьте в эту виртуальную машину сценарий с действиями после отработки отказа для отработки отказа копии зеркала. Обратите внимание, поскольку имя экземпляра базы данных будет изменено, уровень приложения потребуется перенастроить на использование новой базы данных.


#### Настройка сценариев SQL Server для отработки отказа на дополнительный сайт.

1.	Добавьте этот образец сценария в библиотеку VMM на основном и дополнительном сайтах.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Добавьте виртуальную машину с зеркалом базы данных SQL Server в план восстановления, предпочтительно в первую группу загрузки.
3.	С помощью указанного выше сценария VMM добавьте в эту виртуальную машину сценарий с действиями после отработки отказа для отработки отказа копии зеркала. Обратите внимание, поскольку имя экземпляра базы данных будет изменено, уровень приложения потребуется перенастроить на использование новой базы данных.





## Рекомендации по тестированию отработки отказа

Если вы используете группы доступности AlwaysOn, вам не удастся выполнить тестовую отработку отказа на уровне сервера SQL Server. В качестве альтернативы предлагаем следующие варианты.

- Вариант 1

	1. Выполните тестовую отработку отказа на уровне приложений и интерфейсном уровне.
	2. Обновите уровень приложений для доступа к копии реплики в режиме только для чтения и выполните проверку приложения в режиме только для чтения.

- Вариант 2
1.	Создайте копию экземпляра виртуальной машины с репликой SQL Server (с помощью клона VMM типа "сеть-сеть" или функции резервного копирования Azure) и добавьте ее в тестовую сеть.
2.	Выполните тестовую отработку отказа с помощью плана восстановления.

## Рекомендации относительно восстановления размещения

В случае стандартных кластеров SQL восстановление размещения после незапланированной отработки отказа потребуется выполнить резервное копирование сервера SQL Server и его восстановление из экземпляра зеркала в исходном кластере, а затем повторно установить зеркало.





 

<!---HONumber=August15_HO6-->