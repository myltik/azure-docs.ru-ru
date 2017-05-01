---
title: "Развертывание приложения в масштабируемых наборах виртуальных машин Azure | Документация Майкрософт"
description: "Сведения о развертывании простого приложения автомасштабирования в масштабируемом наборе виртуальных машин с помощью шаблона Azure Resource Manager."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: getting-started
ms.date: 4/4/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 8a903cd870f01f9ca6224efd1386b68c63e3aa98
ms.lasthandoff: 04/06/2017


---

# <a name="deploy-an-autoscaling-app-using-a-template"></a>Развертывание приложения автомасштабирования с помощью шаблона

[Шаблоны Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) прекрасно подходят для развертывания группы связанных ресурсов. В этом руководстве, которое составлено на основе статьи [О данном учебнике](virtual-machine-scale-sets-mvss-start.md), описывается развертывание простого приложения автомасштабирования в масштабируемом наборе с помощью шаблона Azure Resource Manager.  Можно также настроить автомасштабирование с помощью PowerShell, интерфейса командной строки или портала. Дополнительные сведения см. в разделе [Как использовать автомасштабирование и масштабируемые наборы виртуальных машин](virtual-machine-scale-sets-autoscale-overview.md).

## <a name="two-quickstart-templates"></a>Два шаблона быстрого запуска
При развертывании масштабируемого набора вы можете установить новое программное обеспечение в образ платформы, используя [расширения виртуальной машины](../virtual-machines/virtual-machines-windows-extensions-features.md). Расширение виртуальной машины — это небольшое приложение, которое выполняет задачи настройки и автоматизации после развертывания виртуальных машин Azure, таких как развертывание приложения. Два различных примера шаблонов приведены в [репозитории azure-quickstart-templates на сайте GitHub](https://github.com/Azure/azure-quickstart-templates), где показано, как развернуть приложение автомасштабирования в масштабируемом наборе с помощью расширений виртуальной машины.

### <a name="python-http-server-on-linux"></a>HTTP-сервер Python под управлением Linux
Пример шаблона [HTTP-сервера Python под управлением Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) развертывает простое приложение автомасштабирования, выполняющееся в масштабируемом наборе Linux.  Веб-платформа [Bottle](http://bottlepy.org/docs/dev/) Python и простой HTTP-сервер развертываются на каждой виртуальной машине в масштабируемом наборе с помощью расширения пользовательского скрипта виртуальной машины. Емкость масштабируемого набора увеличивается, когда средний объем использования ЦП на всех виртуальных машинах превышает 60 %. Если средний объем использования ЦП составляет менее 30 % — емкость масштабируемого набора уменьшается.

Помимо ресурса масштабируемого набора, пример шаблона *azuredeploy.json* также объявляет виртуальную сеть, общедоступный IP-адрес, балансировщик нагрузки и ресурсы параметров автомасшабирования.  Дополнительные сведения о создании ресурсов в шаблоне см. в статье [Автоматическое масштабирование машин Linux в наборе масштабирования виртуальных машин](virtual-machine-scale-sets-linux-autoscale.md).

В шаблоне *azuredeploy.json* свойство `extensionProfile` ресурса `Microsoft.Compute/virtualMachineScaleSets` указывает расширение пользовательского скрипта. `fileUris` указывает расположение скрипта (скриптов). В нашем случае расположение двух файлов: *workserver.py*, который определяет простой HTTP-сервер, и *installserver.sh*, который устанавливает Bottle и запускает HTTP-сервер. `commandToExecute` указывает команду, которую необходимо выполнить после развертывания масштабируемого набора.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Приложение ASP.NET MVC для Windows
Пример шаблона [приложения ASP.NET MVC под управлением Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) развертывает простое приложение ASP.NET MVC, выполняющееся в IIS в масштабируемом наборе Windows.  IIS и приложение MVC развертываются с помощью расширения виртуальной машины для [настройки требуемого состояния PowerShell (DSC)](virtual-machine-scale-sets-dsc.md).  Емкость масштабируемого набора увеличивается (на одной виртуальной машине за раз), когда объем использования ЦП превышает 50 % в течение 5 минут. 

Помимо ресурса масштабируемого набора, пример шаблона *azuredeploy.json* также объявляет виртуальную сеть, общедоступный IP-адрес, балансировщик нагрузки и ресурсы параметров автомасшабирования. В этом шаблоне также показано обновление приложения.  Дополнительные сведения о создании ресурсов в шаблоне см. в статье [Автоматическое масштабирование ВМ в наборе масштабирования ВМ](virtual-machine-scale-sets-windows-autoscale.md).

В шаблоне *azuredeploy.json* свойство `extensionProfile` ресурса `Microsoft.Compute/virtualMachineScaleSets` указывает расширение [настройки требуемого состояния](virtual-machine-scale-sets-dsc.md), которое устанавливает IIS и веб-приложение по умолчанию из пакета WebDeploy.  Скрипт *IISInstall.ps1*, расположенный в папке *DSC*, устанавливает IIS на виртуальной машине.  Веб-приложение MVC находится в папке *WebDeploy*.  Пути к установленному скрипту и веб-приложению определяются в параметрах `powershelldscZip` и `webDeployPackage` в файле *azuredeploy.parameters.json*. 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>Развертывание шаблона
Самый простой способ развернуть шаблон [HTTP-сервера Python под управлением Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) или шаблон [приложения ASP.NET MVC под управлением Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) — использовать кнопку **Deploy to Azure** (Развертывание в Azure), расположенную в файлах сведений на GitHub.  Чтобы развернуть примеры шаблонов, можно также использовать PowerShell или Azure CLI.

### <a name="powershell"></a>PowerShell
Скопируйте из репозитория GitHub файл [HTTP-сервера Python под управлением Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) или [приложения ASP.NET MVC под управлением Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) в папку на локальном компьютере.  Откройте файл *azuredeploy.parameters.json* и обновите значения по умолчанию для параметров `vmssName`, `adminUsername` и `adminPassword`. Сохраните приведенный ниже скрипт PowerShell под именем *deploy.ps1* в той же папке, что и шаблон *azuredeploy.json*. Чтобы развернуть пример шаблона, выполните скрипт *deploy.ps1* в командной строке PowerShell.

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "azuredeploy.json",

 [string]
 $parametersFilePath = "azuredeploy.parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.resources");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
