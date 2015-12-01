<properties
    pageTitle="Отправка данных системы диагностики Azure в Application Insights с помощью PowerShell | Microsoft Azure"
    description="Автоматизируйте настройку системы диагностики Azure для передачи данных в Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Отправка данных системы диагностики Azure в Application Insights с помощью PowerShell

[Microsoft Azure](https://azure.com) можно [настроить для отправки данных системы диагностики Azure](app-insights-azure-diagnostics.md) в [Visual Studio Application Insights](app-insights-overview.md). Данные диагностики связаны с облачными службами Azure и виртуальными машинами Azure. Они дополняют данные телеметрии, отправляемые из приложения с помощью пакета SDK Application Insights. В рамках автоматизации создания новых ресурсов в Azure вы можете настроить диагностику с помощью PowerShell.

## Включение расширения диагностики как части развертывания облачной службы

Параметр `ExtensionConfiguration` командлета `New-AzureDeployment` принимает массив значений для конфигурации диагностики. Их можно создать с помощью командлета `New-AzureServiceDiagnosticsExtensionConfig`. Например:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## Включение расширения диагностики в существующей облачной службе

Для уже существующей службы используйте командлет `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## Получение текущей конфигурации расширения диагностики

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## Удаление расширения диагностики

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Если расширение диагностики было включено с помощью командлета `Set-AzureServiceDiagnosticsExtension` или `New-AzureServiceDiagnosticsExtensionConfig` без параметра Role, вы можете удалить расширение с помощью командлета `Remove-AzureServiceDiagnosticsExtension` без параметра Role. Если параметр Role использовался при включении расширения, он также должен применяться и при его удалении.

Удаление расширения диагностики из каждой отдельной роли

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## Связанные разделы

* [Мониторинг приложений облачных служб Azure с помощью Application Insights](app-insights-cloudservices.md)
* [Отправка данных системы диагностики Azure в Application Insights](app-insights-azure-diagnostics.md)

<!---HONumber=AcomDC_1125_2015-->