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
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.contentlocale: ru-ru
ms.lasthandoff: 08/25/2017

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
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
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
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
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

### <a name="azure-cli"></a>Инфраструктура CLI Azure
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

