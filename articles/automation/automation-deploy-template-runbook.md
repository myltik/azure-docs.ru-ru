---
title: Развертывание шаблона Azure Resource Manager в runbook службы автоматизации Azure
description: Как развернуть шаблон Azure Resource Manager, хранящийся в службе хранилища Azure, из runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell, runbook, json, служба автоматизации azure
ms.openlocfilehash: 489676736e0a3dcff463fae954f0250d90ba3d0f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195443"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Развертывание шаблона Azure Resource Manager в runbook PowerShell службы автоматизации Azure

Можно написать [runbook PowerShell службы автоматизации Azure](automation-first-runbook-textual-powershell.md), который развертывает ресурс Azure с помощью [шаблона управления ресурсами Azure](../azure-resource-manager/resource-manager-create-first-template.md).

Таким образом можно автоматизировать развертывание ресурсов Azure. Можно хранить шаблоны Resource Manager в центральном безопасном сетевом расположении, например в службе хранилища Azure.

В этом разделе мы создадим runbook PowerShell, использующий шаблон Resource Manager, который расположен в [службе хранилища Azure](../storage/common/storage-introduction.md), для развертывания новой учетной записи хранения Azure.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим учебником требуется:

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или <a href="/pricing/free-account/" target="_blank">[зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Учетная запись службы автоматизации](automation-sec-configure-azure-runas-account.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure.  Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* [Учетная запись хранения Azure](../storage/common/storage-create-storage-account.md) для хранения шаблона Resource Manager.
* Azure PowerShell, установленный на локальном компьютере. Дополнительные сведения о получении Azure PowerShell см. в статье [Install and configure Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) (Установка и настройка Azure PowerShell).

## <a name="create-the-resource-manager-template"></a>Создание шаблона Resource Manager

В этом примере мы используем шаблон Resource Manager, который развертывает новую учетную запись хранения Azure.

Скопируйте приведенный ниже текст в текстовый редактор.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Сохраните файл локально как `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Сохранение шаблона Resource Manager в службе хранилища Azure

Теперь мы используем PowerShell, чтобы создать файловый ресурс службы хранилища Azure, и передадим в него файл `TemplateTest.json`.
Инструкции по созданию файлового ресурса и передаче файла на портал Azure см. в разделе [Приступая к работе с хранилищем файлов Azure в Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Запустите PowerShell на локальном компьютере и выполните приведенные команды, чтобы создать файловый ресурс и передать в него шаблон Resource Manager.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Создание сценария runbook PowerShell

Теперь мы создадим сценарий PowerShell, который получает файл `TemplateTest.json` из службы хранилища Azure и развертывает шаблон для создания новой учетной записи хранения Azure.

Вставьте приведенный ниже текст в текстовый редактор.

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Сохраните файл локально как `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Импорт runbook в учетную запись службы автоматизации Azure и его публикация

Теперь мы используем PowerShell, чтобы импортировать runbook в учетную запись службы автоматизации Azure, а затем опубликовать его.
Сведения о том, как импортировать и опубликовать runbook на портале Azure, см. в разделе [Создание или импорт модуля Runbook в службе автоматизации Azure](automation-creating-importing-runbook.md).

Чтобы импортировать `DeployTemplate.ps1` в свою учетную запись автоматизации как runbook PowerShell, выполните следующие команды PowerShell.

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Запуск модуля runbook

Теперь мы запустим runbook, вызвав командлет [AzureRmAutomationRunbook начала](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0).

Сведения о том, как запустить runbook на портале Azure, см. в разделе [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

В консоли PowerShell выполните следующие команды.

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Запустится runbook. Его состояние можно проверить, выполнив команду `$job.Status`.

Модуль runbook получает шаблон Resource Manager и использует его для развертывания новой учетной записи хранения Azure.
Вы увидите, что учетная запись хранения создана, выполнив следующую команду.
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Сводка

Вот и все! Теперь с помощью службы автоматизации Azure, службы хранилища Azure и шаблонов Resource Manager можно развернуть все свои ресурсы Azure.

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о шаблонах Resource Manager см. в статье [Общие сведения о диспетчере ресурсов Azure](../azure-resource-manager/resource-group-overview.md).
* Чтобы приступить к работе со службой хранилища Azure, изучите раздел [Введение в хранилище Microsoft Azure](../storage/common/storage-introduction.md).
* Другие полезные runbook службы автоматизации Azure приведены в разделе [Коллекции модулей Runbook и других модулей для службы автоматизации Azure](automation-runbook-gallery.md).
* Другие полезные шаблоны Resource Manager можно найти на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/).

