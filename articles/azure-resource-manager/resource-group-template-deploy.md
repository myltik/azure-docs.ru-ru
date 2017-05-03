---
title: "Развертывание ресурсов с помощью PowerShell и шаблона | Документация Майкрософт"
description: "Узнайте, как использовать Azure Resource Manager и Azure PowerShell для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f5119fef1fb0a316b4109ccbc4433f8c9a18d128
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell

В этом разделе объясняется, как использовать Azure PowerShell с шаблонами Resource Manager для развертывания ресурсов в Azure. Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI).

Вы можете получить шаблон (storage.json), используемый в этих примерах, в статье [Создание первого шаблона Azure Resource Manager](resource-manager-create-first-template.md#final-template). Чтобы использовать шаблон с этими примерами, создайте JSON-файл и добавьте в него скопированное содержимое.

## <a name="deploy-local-template"></a>Развертывание локального шаблона
Чтобы быстро приступить к развертыванию, используйте следующие командлеты для развертывания локального шаблона со встроенными параметрами. 

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

Развертывание может занять несколько минут. По завершении появится сообщение, содержащее результат:

```powershell
ProvisioningState       : Succeeded
```

Предыдущий пример создавал группу ресурсов в подписке по умолчанию. Чтобы использовать другую подписку, добавьте командлет [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) после входа.

## <a name="deploy-external-template"></a>Развертывание внешнего шаблона

Для развертывания внешнего шаблона используйте параметр **TemplateUri**. Шаблон может иметь любой общедоступный код URI (например, быть файлом в учетной записи хранения).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -storageNamePrefix contoso -storageSKU Standard_GRS
```

Шаблон можно защитить, запрашивая токен подписанного URL-адреса (SAS) для доступа. Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).

## <a name="parameter-files"></a>Файлы параметров

В предыдущих примерах показано, как передать параметры в виде встроенных значений. Вы можете указать значения параметров в файле и передать его при развертывании. 

Файл параметров должен быть в указанном ниже формате.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

Чтобы передать локальный файл параметров, используйте параметр **TemplateParameterFile**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Чтобы передать внешний файл параметров, используйте параметр **TemplateParameterUri**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.parameters.json
```

Вы можете использовать в ходе одной операции развертывания как встроенные параметры, так и локальный файл параметров. Например, часть значений можно указать в локальном файле параметров, а другую часть — в команде развертывания. Если значения для одного параметра указаны одновременно и в локальном файле параметров, и в командной строке, более высокий приоритет имеет значение из командной строки.

Тем не менее при использовании внешнего файла параметров нельзя передавать другие значения в командной строке или локальном файле. Если в параметре **TemplateParameterUri** указан файл параметров, все параметры командной строки игнорируются. Все значения параметров следует указать во внешнем файле. Если в шаблоне используется конфиденциальное значение, которое нельзя включать в файл параметров, то его можно передать в хранилище ключей. Кроме того, можно динамически указать значения всех параметров в командной строке.

Если шаблон содержит параметр, имя которого совпадает с именем одного из параметров в команде PowerShell, параметр из шаблон отображается с постфиксом **FromTemplate**. Предположим, что параметр **ResourceGroupName** в шаблоне конфликтует с параметром **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Вам будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**. В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

## <a name="test-a-deployment"></a>Тестирование развертывания

Чтобы проверить шаблон и значения параметров без фактического развертывания ресурсов, используйте [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). Он имеет те же параметры для использования локальных или удаленных файлов.

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

## <a name="log-deployment-content-for-debugging"></a>Регистрация сведений о развертывании для отладки

Сведения об операциях развертывания автоматически регистрируется в журналах действий. Но чтобы включить в журнал дополнительные сведения о развертывании, которые могут помочь в устранении ошибок развертывания, используйте параметр **DeploymentDebugLogLevel**. Можно задать регистрацию в журнале содержимого запроса или содержимого ответа (или и того, и другого) при операции развертывания.
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All `
  -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

Дополнительные сведения о просмотре журналов см. в статье [Просмотр журналов действий для аудита действий с ресурсами](resource-group-audit.md).

## <a name="export-resource-manager-template"></a>Экспорт шаблона Resource Manager
Для существующей группы ресурсов (развернутой с помощью PowerShell или других средств, например через портал) можно просмотреть шаблон Resource Manager. Экспорт шаблона обеспечивает два преимущества:

1. Последующие развертывания решения можно с легкостью автоматизировать, так как в шаблоне определены все компоненты инфраструктуры.
2. Вы можете ознакомиться с синтаксисом шаблона, просмотрев представление JSON решения.

Чтобы просмотреть шаблон для группы ресурсов, выполните командлет [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) .

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleResourceGroup
```

Дополнительные сведения см. в статье [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Чтобы использовать полный режим, используйте параметр `Mode`.

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```


## <a name="next-steps"></a>Дальнейшие действия
* Полный пример сценария, развертывающего шаблон, см. в статье [Сценарий для развертывания шаблона Resource Manager](resource-manager-samples-powershell-deploy.md).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).


