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
ms.date: 03/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5b8b293b5b37365088a3df55581be7b7bf76691c
ms.openlocfilehash: 7bc3421e00215ca4629ea11811c98e581377b24a
ms.lasthandoff: 01/18/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Интерфейс командной строки Azure](resource-group-template-deploy-cli.md)
> * [Портал](resource-group-template-deploy-portal.md)
> * [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)
> 
> 

В этом разделе объясняется, как использовать Azure PowerShell с шаблонами Resource Manager для развертывания ресурсов в Azure. Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

> [!TIP]
> Справку по отладке ошибок во время развертывания можно получить в следующих статьях.
> 
> * Статья [Просмотр операций развертывания с помощью Azure Resource Manager](resource-manager-deployment-operations.md) содержит информацию о том, как получить сведения, которые помогут устранить ошибку.
> * [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md) — руководство по устранению распространенных ошибок при развертывании.
> 
> 

## <a name="quick-steps-to-deployment"></a>Быстрое развертывание
Чтобы быстро приступить к выполнению развертывания, воспользуйтесь следующими командами:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json -TemplateParameterFile c:\MyTemplates\example.params.json
```

Эти команды создают группу ресурсов и развертывают в ней шаблон. Файл шаблона и файл параметров являются локальными. Если это вам подходит, то все необходимое готово к развертыванию ресурсов. Тем не менее, существуют дополнительные параметры, с помощью которых можно указать развертываемые ресурсы. В оставшейся части этой статьи описаны все доступные во время развертывания параметры. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Развернуть
1. Войдите в свою учетную запись Azure.

   ```powershell
   Login-AzureRmAccount
   ```

    Возвращается сводка по учетной записи.
    
   ```powershell
   Environment           : AzureCloud
   Account               : someone@example.com
   TenantId              : {guid}
   SubscriptionId        : {guid}
   SubscriptionName      : Example Subscription
   CurrentStorageAccount :
   ```

2. Если у вас несколько подписок, укажите идентификатор той, которая будет использоваться для развертывания, с помощью команды **Set-AzureRmContext**. 
   
   ```powershell
   Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
   ```
3. При развертывании шаблона необходимо указать группу ресурсов, которая будет содержать развернутые ресурсы. Если у вас уже есть группа ресурсов, в которую можно развернуть шаблон, пропустите этот шаг и используйте существующую группу. 
   
     Чтобы создать группу ресурсов, укажите ее имя и расположение. Следует указать расположение группы ресурсов, так как она хранит метаданные ресурсов. Для соответствия требованиям вам, возможно, нужно указать, где хранятся эти метаданные. Обычно мы рекомендуем указывать расположение, в котором будет храниться большинство ресурсов. Используя одно и то же расположение, можно упростить шаблон.
   
   ```powershell
   New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   ```
   
     Появится сводка по новой группе ресурсов.
4. Перед выполнением развертывания можно проверить соответствующие параметры. С помощью командлета **Test-AzureRmResourceGroupDeployment** можно выявить проблемы до создания фактических ресурсов. В следующем примере показано, как проверить развертывание.
   
   ```powershell
   Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>
   ```
5. Чтобы развернуть ресурсы в группе ресурсов, выполните командлет **New-AzureRmResourceGroupDeployment** и укажите необходимые параметры. имя развертывания, имя группы ресурсов, путь или URL-адрес шаблона, а также другие необходимые вам параметры. Если параметр **Режим** не указан, используется стандартное значение **Добавочный**. Чтобы выполнить полное развертывание, установите для параметра **mode** значение **Complete**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.
   
     Для развертывания локального шаблона используйте параметр **TemplateFile**.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json
   ```
   
     Для развертывания внешнего шаблона используйте параметр **TemplateUri**.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json
   ```
   
     В двух предыдущих примерах не указаны значения параметров. Варианты передачи значений параметров в рассматриваются в разделе [Параметры](#parameters). Пока что указать значения параметров будет предложено посредством следующего синтаксиса.

   ```powershell  
   cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
   Supply values for the following parameters:
   (Type !? for Help.)
   firstParameter: <type here>
   ```
     
     После развертывания ресурсов вы увидите сводку по развертыванию. Сводка содержит значение **ProvisioningState**, указывающее, успешно ли выполнено развертывание.

   ```powershell   
   DeploymentName    : ExampleDeployment
   ResourceGroupName : ExampleResourceGroup
   ProvisioningState : Succeeded
   Timestamp         : 4/14/2015 7:00:27 PM
   Mode              : Incremental
   ```
      
6. Если вы хотите включить в журнал дополнительные сведения о развертывании, которые могут помочь в устранении ошибок развертывания, используйте параметр **DeploymentDebugLogLevel**. Можно задать регистрацию в журнале содержимого запроса или содержимого ответа (или и того, и другого) при операции развертывания.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
   ```
   
     Дополнительные сведения об отладке содержимого для устранения неполадок при развертывании см. в статье [Просмотр операций развертывания с помощью Azure Resource Manager](resource-manager-deployment-operations.md).

## <a name="deploy-private-template-with-sas-token"></a>Развертывание частного шаблона с помощью маркера SAS
Шаблоны можно добавить в учетную запись хранения и создать ссылки на них во время развертывания с помощью маркера SAS.

> [!IMPORTANT]
> Если выполнить приведенные ниже действия, то большой двоичный объект, содержащий шаблон, будет доступен только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Применение маркера SAS — хороший способ ограничить доступ к своим шаблонам, но не следует указывать конфиденциальные данные, например пароли, непосредственно в шаблоне.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Добавление частного шаблона в учетную запись хранения
Ниже приведены действия по настройке учетной записи хранения для шаблонов.

1. Создайте группу ресурсов.
   
   ```powershell
   New-AzureRmResourceGroup -Name ManageGroup -Location "West US"
   ```
2. Создайте учетную запись хранения. Имя учетной записи хранения должно быть уникальным в Azure, поэтому введите собственное имя для учетной записи.
   
   ```powershell
   New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"
   ```
3. Задайте текущую учетную запись хранения.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
4. Создайте контейнер. Разрешение имеет значение **Off** , это означает, что контейнер доступен только владельцу.
   
   ```powershell
   New-AzureStorageContainer -Name templates -Permission Off
   ```
5. Добавьте свой шаблон в контейнер.
   
   ```powershell
   Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Предоставление маркера SAS во время развертывания
Чтобы развернуть частный шаблон в учетной записи хранения, извлеките маркер SAS и добавьте его в универсальный код ресурса (URI) для шаблона.

1. Если вы изменили текущую учетную запись хранения, выберите текущей учетную запись хранения, содержащую ваши шаблоны.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
2. Создайте маркер SAS с разрешениями на чтение и сроком действия, чтобы ограничить доступ. Извлеките полный универсальный код ресурса (URI) шаблона с маркером SAS.
   
   ```powershell
   $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
   ```
3. Разверните шаблон, указав универсальный код ресурса (URI), который включает в себя маркер SAS.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri
   ```

Пример использования маркера SAS со связанными шаблонами см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>Параметры

Для предоставления значений параметров доступны следующие способы: 
   
- Встроенные параметры. Добавьте в командлет имена отдельных параметров (например, **-myParameterName**).

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"
   ```
- Объект параметра. Добавьте параметр **-TemplateParameterObject**.

   ```powershell   
   $parameters = @{"<ParameterName>"="<Parameter Value>"}
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters
   ```
- Локальный файл параметров. Добавьте параметр **-TemplateParameterFile**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile c:\MyTemplates\example.params.json
   ```
- Внешний файл параметров. Добавьте параметр **-TemplateParameterUri**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.params.json
   ```
      
[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)] 

Если шаблон содержит параметр, имя которого совпадает с именем одного из параметров в команде PowerShell, появится окно с запросом указать значение для этого параметра. Azure PowerShell представляет параметр из шаблона, добавляя к нему постфикс **FromTemplate**. Предположим, что параметр **ResourceGroupName** в шаблоне конфликтует с параметром **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Вам будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**. В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

## <a name="parameter-precedence"></a>Приоритет параметров

Вы можете использовать в ходе одной операции развертывания как встроенные параметры, так и локальный файл параметров. Например, часть значений можно указать в локальном файле параметров, а другую часть — в команде развертывания. Если значения для одного параметра указаны одновременно и в локальном файле параметров, и в командной строке, более высокий приоритет имеет значение из командной строки.

Тем не менее при использовании внешнего файла параметров нельзя передавать другие значения в командной строке или локальном файле. Если в параметре **TemplateParameterUri** указан файл параметров, все параметры командной строки игнорируются. Все значения параметров следует указать во внешнем файле. Если в шаблоне используется конфиденциальное значение, которое нельзя включать в файл параметров, то его можно передать в хранилище ключей. Кроме того, можно динамически указать значения всех параметров в командной строке.

## <a name="next-steps"></a>Дальнейшие действия
* Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
* Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).
* Сведения об автоматизации развертывания см. в статье [Automating application deployments to Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Автоматизация развертывания приложений на виртуальных машинах Azure), которая входит в цикл из четырех частей. Этот цикл охватывает такие аспекты, как архитектура приложения, доступ и безопасность, доступность и масштабирование, а также развертывание приложения.


