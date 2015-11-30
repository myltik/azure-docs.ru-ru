<properties 
	pageTitle="Включение диагностики в облачных службах Azure с помощью PowerShell | Microsoft Azure " 
	description="Узнайте, как включить диагностику для облачных служб с помощью PowerShell." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="saurabh"/>


# Включение диагностики в облачных службах Azure с помощью PowerShell

Для сбора диагностических данных, таких как журналы приложений, счетчики производительности и т. д., из облачной службы можно использовать расширение диагностики Azure. В этой статье описывается включение расширения диагностики Azure для облачной службы с помощью PowerShell. Сведения о компонентах, которые потребуются для выполнения инструкций в этой статье, см. в разделе [Установка и настройка Azure PowerShell](powershell-install-configure.md).

## Включение расширения диагностики как части развертывания облачной службы

Этот подход хорошо подходит для сценариев непрерывной интеграции, где можно включить расширение диагностики. Расширение диагностики можно включить в процессе развертывания облачной службы, передав параметр *ExtensionConfiguration* в командлет [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx). Параметр *ExtensionConfiguration* принимает массив конфигураций диагностики, которые могут быть созданы с помощью командлета [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx).

В следующем примере показано включение диагностики для облачной службы с веб-ролью и рабочей ролью, каждая из которых имеет свою конфигурацию диагностики.

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	  
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 



## Включение расширения диагностики в существующей облачной службе

Для включения диагностики в уже работающей облачной службе можно использовать командлет [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx).


	$service_name = "MyService"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"
 

## Получение текущей конфигурации расширения диагностики
Получите текущую конфигурацию диагностики для облачной службы с помощью командлета [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx).
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## Удаление расширения диагностики
Чтобы отключить диагностику в облачной службе, используйте командлет [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx).

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Если расширение диагностики было включено с помощью командлета *Set-AzureServiceDiagnosticsExtension* или *New-AzureServiceDiagnosticsExtensionConfig* без параметра *Role*, его можно удалить с помощью командлета *Remove-AzureServiceDiagnosticsExtension* без параметра *Role*. Если параметр *Role* использовался при включении расширения, он также должен применяться при удалении расширения.

Удаление расширения диагностики из каждой отдельной роли

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## Дальнейшие действия

- Дополнительные рекомендации по использованию диагностики Azure и других методов для устранения неполадок см. в статье [Включение диагностики в облачных службах и виртуальных машинах Azure](cloud-services-dotnet-diagnostics.md).
- В статье [Схема конфигурации диагностики](https://msdn.microsoft.com/library/azure/dn782207.aspx) поясняются различные параметры XML-конфигураций для расширения диагностики.
- Сведения о включении расширения диагностики для виртуальных машин см. в статье [Создание виртуальной машины Windows с мониторингом и диагностикой с помощью шаблона диспетчера ресурсов Azure](virtual-machines-extensions-diagnostics-windows-template.md)  

<!---HONumber=Nov15_HO4-->