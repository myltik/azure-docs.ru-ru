---
title: Развертывание рабочей области машинного обучения с помощью Azure Resource Manager | Документация Майкрософт
description: Развертывание рабочей области для Машинного обучения Azure с помощью шаблона Azure Resource Manager
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/05/2018
ms.openlocfilehash: 82d2316b3f72fbb0c5c3ee1ea9424afcc7661361
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833977"
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Развертывание рабочей области машинного обучения с помощью Azure Resource Manager
## <a name="introduction"></a>Введение
Шаблон развертывания Azure Resource Manager позволяет сэкономить время, предоставляя масштабируемый способ развертывания взаимосвязанных компонентов с возможностью проверки и механизмом повтора. Чтобы настроить рабочие области Машинного обучения Azure, например, сначала необходимо настроить учетную запись хранения Azure, а затем развернуть рабочую область. Представьте себе выполнение этого задания вручную для сотен рабочих областей. Простой альтернативой является развертывание рабочей области машинного обучения Azure и всех ее зависимых компонентов с помощью шаблона Azure Resource Manager. В этой статье представлено пошаговое выполнение этого процесса. Подробный обзор Azure Resource Manager см. в статье [Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Пошаговое создание рабочей области машинного обучения
Сначала мы создадим группу ресурсов Azure, а затем развернем новую учетную запись хранения Azure и рабочую область машинного обучения Azure с помощью шаблона Resource Manager. После завершения развертывания мы выведем важные сведения о созданных рабочих областях (первичный ключ, идентификатор и URL-адрес рабочей области).

### <a name="create-an-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager
Рабочей области машинного обучения требуется учетная запись хранения Azure для хранения связанного с ней набора данных.
В следующем шаблоне для создания имени учетной записи хранения и рабочей области используется имя группы ресурсов.  Имя учетной записи хранения также используется в качестве свойства при создании рабочей области.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Сохраните этот шаблон как файл mlworkspace.json в папке C:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Развертывание группы ресурсов на основе шаблона
* Откройте PowerShell.
* Установите модули для Azure Resource Manager и управления службами Azure.  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   При этом скачиваются и устанавливаются модули, необходимые для выполнения оставшихся действий. Их достаточно выполнить один раз в среде выполнения команд PowerShell.   

* Проверка подлинности в Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Connect-AzureRmAccount
```
Этот шаг необходимо выполнять для каждого сеанса. После выполнения проверки подлинности должны отображаться сведения о подписке.

![Учетная запись Azure][1]

Получив доступ к Azure, мы можем создать группу ресурсов.

* Создание группы ресурсов

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Убедитесь, что группа ресурсов должным образом подготовлена к работе. **ProvisioningState** должно отображаться состояние Succeeded.
Шаблон использует имя группы ресурсов для создания имени учетной записи хранения. Имя учетной записи хранения должно содержать от 3 до 24 знаков и состоять только из цифр и букв нижнего регистра.

![Группа ресурсов][2]

* Разверните новую рабочую область машинного обучения с помощью развертывания группы ресурсов.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

После завершения развертывания очень просто получить доступ к свойствам развернутой рабочей области. Например, можно получить доступ к сведениям о маркере первичного ключа.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Другой способ получения маркеров существующей рабочей области — использовать команду Invoke-AzureRmResourceAction. Например, можно отобразить список основных и дополнительных маркеров всех рабочих областей.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
После подготовки рабочей области к работе можно автоматизировать многие задачи Студии машинного обучения Azure с помощью [модуля PowerShell для Машинного обучения Azure](http://aka.ms/amlps).

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о [создании шаблонов Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Просмотрите [репозиторий шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates). 
* Просмотрите видео об [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
