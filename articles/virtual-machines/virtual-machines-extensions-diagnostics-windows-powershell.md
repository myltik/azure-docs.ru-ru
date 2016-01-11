<properties pageTitle="Включение диагностики на виртуальной машине Azure под управлением Windows с помощью PowerShell | Microsoft Azure" description="Узнайте, как включить диагностику на виртуальной машине Azure под управлением Windows, используя PowerShell" services="virtual-machines" documentationCenter="" authors="sbtron" manager="" editor="""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="saurabh"/>


# Включение диагностики на виртуальной машине Azure под управлением Windows с помощью PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Для сбора диагностических данных, таких как журналы приложений, счетчики производительности и т. д., с виртуальной машины Azure под управлением Windows вы можете использовать расширение диагностики Azure. В этой статье описывается включение расширения диагностики Azure для виртуальной машины с помощью PowerShell. Сведения о компонентах, которые потребуются для выполнения инструкций в этой статье, см. в разделе [Установка и настройка Azure PowerShell](powershell-install-configure.md).

## Включение расширения диагностики Azure на виртуальной машине с использованием модели развертывания диспетчера ресурсов

Вы можете включить расширение диагностики при создании виртуальной машины Windows, используя модель развертывания диспетчера ресурсов. Для этого конфигурацию расширения нужно добавить в шаблон диспетчера ресурсов. См. статью [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона диспетчера ресурсов Azure](virtual-machines-extensions-diagnostics-windows-template.md).

Чтобы включить расширение диагностики Azure на уже существующей виртуальной машине, созданной с помощью модели развертывания диспетчера ресурсов, вы можете использовать командлет PowerShell [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx), как показано ниже.


	$vm_resourcegroup = "myvmresourcegroup"
	$vm_name = "myvm"
	$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"
	
	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path 


*$diagnosticsconfig\_path* — это путь к XML-файлу с конфигурацией диагностики, как в приведенном ниже [примере](#sample-diagnostics-configuration).

Если файл конфигурации диагностики содержит элемент **StorageAccount** с именем учетной записи хранения, сценарий *Set-AzureRMVMDiagnosticsExtension* автоматически настраивает расширение диагностики на отправку диагностических данных в эту учетную запись. Для этого учетная запись хранения должна входить в ту же подписку, что и виртуальная машина.

Если в конфигурации диагностики нет элемента **StorageAccount**, в командлет необходимо передать параметр *StorageAccountName*. Если параметр *StorageAccountName* указан, командлет использует учетную запись хранения, указанную в параметре, а не заданную в файле конфигурации диагностики.

Если учетная запись хранения диагностики и виртуальная машина относятся к разным подпискам, в командлет необходимо отдельно передать параметры *StorageAccountName* и *StorageAccountKey*. Параметр *StorageAccountKey* не требуется, если диагностическая учетная запись хранения входит в ту же подписку, поскольку при включении расширения диагностики командлет автоматически запрашивает и устанавливает значение ключа. Если же диагностическая учетная запись хранения входит в другую подписку, командлет не сможет получить ключ автоматически, а значит, его необходимо указать с помощью параметра *StorageAccountKey*.

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key	

Если расширение диагностики Azure на виртуальной машине включено, получить текущие параметры позволяет командлет [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx).

	Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Возвращенное командлетом значение *PublicSettings* содержит XML-файл конфигурации в кодировке Base64. Чтобы прочитать XML-файл, его необходимо декодировать.
	
	$publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
	$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
	$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
	Write-Host $xmlconfig
 
Для удаления расширения диагностики с виртуальной машины вы можете использовать командлет [Remove-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx).
  
## Включение расширения диагностики Azure на виртуальной машине с использованием классической модели развертывания

Включить расширение диагностики Azure на виртуальной машине, созданной на основе классической модели развертывания, позволяет командлет [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx). В следующем примере показано, как создать новую виртуальную машину, используя классическую модель развертывания с включенным расширением диагностики Azure.

	$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
	$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
	$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Чтобы включить расширение диагностики Azure на уже существующей (классической) виртуальной машине, прежде всего получите конфигурацию виртуальной машины с помощью командлета [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx). Затем обновите конфигурацию виртуальной машины, чтобы включить расширение диагностики Azure, с помощью командлета [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx). И наконец, примените обновленную конфигурацию к виртуальной машине с помощью командлета [Update-AzureVM](https://msdn.microsoft.com/library/mt589121.aspx).

	$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
	$VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## Пример конфигурации диагностики

Представленный ниже XML-код можно использовать для открытой конфигурации диагностики с применением описанных выше сценариев. Конфигурация в данном примере передает в диагностическую учетную запись хранения различные счетчики производительности вместе с ошибками из журналов приложений, событий безопасности и системных каналов в Windows, а также из журналов инфраструктуры диагностики.

Конфигурацию необходимо обновить, чтобы включить в нее следующее:

- Атрибут *ResourceID* элемента **Metrics** необходимо обновить, указав идентификатор ресурса для виртуальной машины. 
	- Идентификатор ресурса может иметь следующий формат: "/subscriptions/{*идентификатор подписки, в которую входит виртуальная машина*}/resourceGroups/{*имя группы ресурсов виртуальной машины*}/providers/Microsoft.Compute/virtualMachines/{*имя виртуальной машины*}". 
	- Например, если подписка, в которую входит виртуальная машина, имеет идентификатор **11111111-1111-1111-1111-111111111111**, группа ресурсов называется **MyResourceGroup**, а виртуальная машина — **MyWindowsVM**, атрибут *resourceID* будет выглядеть следующим образом:
	 
		```
		<Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
		```
	- Дополнительные сведения о генерировании метрик на основе счетчиков производительности и конфигурации метрик см. в разделе [Таблица метрик WAD в хранилище](virtual-machines-extensions-diagnostics-windows-template.md#wadmetrics-tables-in-storage).

- Элемент **StorageAccount** необходимо обновить, указав диагностическое имя учетной записи хранения.
 
	```
	<?xml version="1.0" encoding="utf-8"?>
	<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
	        <PerformanceCounters scheduledTransferPeriod="PT1M">
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU utilization" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU privileged time" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU user time" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
	        <annotation displayName="CPU frequency" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Processes" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Threads" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Handles" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Memory usage" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory available" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory committed" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory commit limit" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory paged pool" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory non-paged pool" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active time" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active read time" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active write time" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk operations" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk read operations" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk write operations" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk speed" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk read speed" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk write speed" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average queue length" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average read queue length" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average write queue length" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk free space (percentage)" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk free space (MB)" locale="ru-RU"/>
	      </PerformanceCounterConfiguration>
	    </PerformanceCounters>
		<Metrics resourceId="(Update with resource ID for the VM)" >
	        <MetricAggregation scheduledTransferPeriod="PT1H"/>
	        <MetricAggregation scheduledTransferPeriod="PT1M"/>
	    </Metrics>
	    <WindowsEventLog scheduledTransferPeriod="PT1M">
	      <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
	      <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
	      <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
	    </WindowsEventLog>
	      </DiagnosticMonitorConfiguration>
	    </WadCfg>
	    <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
	</PublicConfig>
	```

## Дальнейшие действия 
- Дополнительные рекомендации по использованию диагностики Azure и других методов для устранения неполадок см. в статье [Включение диагностики в облачных службах и виртуальных машинах Azure](cloud-services-dotnet-diagnostics.md).
- В статье [Схема конфигурации диагностики](https://msdn.microsoft.com/library/azure/mt634524.aspx) поясняются различные параметры XML-конфигураций для расширения диагностики.

<!---HONumber=AcomDC_1223_2015-->