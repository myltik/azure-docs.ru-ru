---
title: "Включение диагностики в облачных службах Azure с использованием PowerShell | Документация Майкрософт"
description: "Узнайте, как включить диагностику для облачных служб с помощью PowerShell."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 032ac0cf678dad3ab544d6e9257b65cf01f36385
ms.openlocfilehash: 3c4d41689fc3c127c60e6e0988d1efe60d4d71b8


---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Включение диагностики в облачных службах Azure с помощью PowerShell 
Для сбора диагностических данных, таких как журналы приложений, счетчики производительности и т. д., из облачной службы можно использовать расширение диагностики Azure. В этой статье описывается включение расширения диагностики Azure для облачной службы с помощью PowerShell.  Сведения о компонентах, которые потребуются для выполнения инструкций в этой статье, см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Включение расширения диагностики как части развертывания облачной службы
Этот подход удобен для сценариев непрерывной интеграции, в которых в ходе развертывания облачной службы можно включить расширение системы диагностики. При развертывании новой облачной службы можно включить расширение системы диагностики, передав параметр *ExtensionConfiguration* командлету [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) . Параметр *ExtensionConfiguration* принимает массив конфигураций диагностики, которые могут быть созданы с помощью командлета [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) .

В следующем примере показано включение диагностики для облачной службы с веб-ролью и рабочей ролью, каждая из которых имеет свою конфигурацию диагностики.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Если файл конфигурации диагностики содержит элемент StorageAccount с именем учетной записи хранения, то командлет New-AzureServiceDiagnosticsExtensionConfig автоматически использует эту учетную запись хранения. Для этого учетная запись хранения должна входить в ту же подписку, что и развертываемая облачная служба.

В пакете SDK Azure 2.6 и более поздних версий файлы конфигурации расширения, создаваемые выходной целью публикации MSBuild, будут включать имя учетной записи хранения на основе строки конфигурации диагностики, указанной в файле конфигурации службы (.cscfg). Приведенный ниже сценарий показывает, как анализировать файлы конфигурации расширения из выходной цели публикации и настраивать расширение системы диагностики для каждой роли при развертывании облачной службы.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online использует аналогичный подход для автоматических развертываний облачных служб с расширением системы диагностики. Полный пример см. в файле [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1).

Если в конфигурации диагностики нет элемента StorageAccount, в командлет необходимо передать параметр StorageAccountName. Если параметр StorageAccountName указан, командлет использует учетную запись хранения, указанную в этом параметре, а не в файле конфигурации диагностики.

Если учетная запись хранения диагностики и облачная служба относятся к разным подпискам, то в командлет необходимо явным образом передать параметры StorageAccountName и StorageAccountKey. Параметр StorageAccountKey не требуется, если учетная запись хранения диагностики входит в ту же подписку, так как при включении расширения системы диагностики командлет автоматически запрашивает и устанавливает значение ключа. Если же учетная запись хранения диагностики входит в другую подписку, то командлет не сможет получить ключ автоматически, а значит, его необходимо явно указать с помощью параметра StorageAccountKey.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Включение расширения диагностики в существующей облачной службе
Для включения или обновления конфигурации диагностики в уже работающей облачной службе можно использовать командлет [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) .

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Получение текущей конфигурации расширения диагностики
Получите текущую конфигурацию диагностики для облачной службы с помощью командлета [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) .

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Удаление расширения диагностики
Чтобы отключить диагностику в облачной службе, используйте командлет [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Если расширение диагностики было включено с использованием командлета *Set-AzureServiceDiagnosticsExtension* или *New-AzureServiceDiagnosticsExtensionConfig* без параметра *Role*, его можно удалить с помощью командлета *Remove-AzureServiceDiagnosticsExtension* без параметра *Role*. Если параметр *Role* использовался при включении расширения, он также должен применяться при удалении расширения.

Удаление расширения диагностики из каждой отдельной роли

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные рекомендации по использованию диагностики Azure и других методов для устранения неполадок см. в статье [Включение диагностики в облачных службах и виртуальных машинах Azure](cloud-services-dotnet-diagnostics.md).
* В статье [Схема конфигурации диагностики](https://msdn.microsoft.com/library/azure/dn782207.aspx) поясняются различные параметры XML-конфигураций для расширения диагностики.
* Сведения о включении расширения диагностики для виртуальных машин см. в статье [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).



<!--HONumber=Nov16_HO3-->


