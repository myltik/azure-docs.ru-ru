---
title: "Связывание шаблонов для развертывания Azure | Документация Майкрософт"
description: "Описывает, как использовать связанные шаблоны в шаблоне диспетчера ресурсов Azure для создания решения модульных шаблонов. Показывает, как передавать значения параметров, указывать файл параметров и динамически создаваемые URL-адреса."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 8b58a83ffd473500dd3f76c09e251f9208527d4f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Использование связанных шаблонов в при развертывании ресурсов Azure
Один шаблон Azure Resource Manager можно связать с другим шаблоном, что позволяет разбивать развертывания на несколько целевых шаблонов с определенным назначением. Как и при разбивке приложения на несколько классов, декомпозиция развертывания гораздо удобнее в контексте тестирования, повторного использования и удобства чтения.  

Можно передавать параметры из основного шаблона в связанный шаблон. Кроме того, эти параметры можно напрямую сопоставить с параметрами или переменными, предоставляемыми вызывающим шаблоном. Связанный шаблон может также передать выходную переменную в исходный шаблон, что позволяет активировать двусторонний обмен данными между шаблонами.

## <a name="linking-to-a-template"></a>Создание связи с шаблоном
Чтобы создать связь между двумя шаблонами, добавьте ресурс развертывания в основном шаблоне, который указывает на связанный шаблон. Задайте для свойства **templateLink** URI связанного шаблона. Значения параметров для связанного шаблона можно указать непосредственно в шаблоне или в файле параметров. В следующем примере используется свойство **parameters** для непосредственного указания значения параметра.

```json
"resources": [ 
  { 
      "apiVersion": "2017-05-10", 
      "name": "linkedTemplate", 
      "type": "Microsoft.Resources/deployments", 
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": { 
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
        } 
      } 
  } 
] 
```

Как и для прочих типов ресурсов, вы можете задать зависимости между связанным шаблоном и другими ресурсами. Следовательно, если другим ресурсам требуется выходное значение из связанного шаблона, то вы можете обеспечить развертывание данного шаблона прежде этих ресурсов. Или, если связанный шаблон зависит от других ресурсов, то вы можете обеспечить их развертывание перед развертыванием связанного шаблона. Извлечь значение из связанного шаблона можно с помощью приведенного ниже синтаксиса.

```json
"[reference('linkedTemplate').outputs.exampleProperty.value]"
```

Служба Resource Manager должна иметь доступ к связанному шаблону. В качестве связанного шаблона нельзя указать локальный файл или файл, доступный только в локальной сети. Можно предоставить только универсальный код ресурса (URI), который включает в себя **http** или **https**. Один из вариантов — разместить связанный шаблон в учетной записи хранения и использовать универсальный код ресурса (URI) этого элемента, как показано в примере ниже.

```json
"templateLink": {
    "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
    "contentVersion": "1.0.0.0",
}
```

Хотя связанный шаблон должны быть доступен извне, он не должен быть общедоступным. Шаблон можно добавить к личной учетной записи хранения, доступ к которой есть только у владельца учетной записи хранения. Затем создайте маркер подписанного URL-адреса для использования при развертывании. Этот маркер SAS добавляется в универсальный код ресурса (URI) связанного шаблона. Действия по настройке шаблона в учетной записи хранения и созданию маркера SAS описаны в разделах [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md) и [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](resource-group-template-deploy-cli.md). 

В следующем примере показан родительский шаблон, связанный с другим шаблоном. Для доступа к связанному шаблону используется маркер SAS, который передается в качестве параметра.

```json
"parameters": {
    "sasToken": { "type": "securestring" }
},
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "linkedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
            "contentVersion": "1.0.0.0"
          }
        }
    }
],
```

Несмотря на то, что маркер передается в защищенной строке, универсальный код ресурса (URI) связанного шаблона, включающий в себя маркер SAS, добавляется в журнал операций развертывания. Чтобы снизить риск раскрытия, задайте срок действия маркера.

Resource Manager обрабатывает каждый связанный шаблон как отдельное развертывание. В журнале развертывания группы ресурсов отображаются отдельные операции развертывания для родительских и вложенных шаблонов.

![журнал развертываний](./media/resource-group-linked-templates/linked-deployment-history.png)

## <a name="linking-to-a-parameter-file"></a>Создание связи с файлом параметров
В следующем примере используется свойство **parametersLink** привязки к файлу параметров.

```json
"resources": [ 
  { 
     "apiVersion": "2017-05-10", 
     "name": "linkedTemplate", 
     "type": "Microsoft.Resources/deployments", 
     "properties": { 
       "mode": "incremental", 
       "templateLink": {
          "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       }, 
       "parametersLink": { 
          "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
          "contentVersion":"1.0.0.0"
       } 
     } 
  } 
] 
```

Значением универсального кода ресурса (URI) для связанного файла параметров не может быть локальный файл, оно должно содержать **http** или **https**. Доступ к файлу параметров также можно ограничить с помощью маркера SAS.

## <a name="using-variables-to-link-templates"></a>Использование переменных для связывания шаблонов
В предыдущих примерах были показаны жестко запрограммированные значения URL-адреса для ссылок на шаблоны. Этот подход может подойти для простого шаблона, но он не действует при работе с большим набором модульных шаблонов. Вместо этого можно создать статическую переменную, которая содержит базовый URL-адрес для основного шаблона, а затем динамически создавать URL-адреса для связанных шаблонов на основе этого адреса. Преимущество этого подхода заключается в том, что можно легко переместить или разветвить шаблон, так как для этого необходимо лишь изменить статическую переменную в основном шаблоне. Основной шаблон передает правильные URI через разделенный шаблон.

В следующем примере показано, как использовать базовый URL-адрес для создания двух URL-адресов для связанных шаблонов (**sharedTemplateUrl** и **vmTemplate**). 

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Вы также можете получить базовый URL-адрес текущего шаблона с помощью функции [deployment()](resource-group-template-functions-deployment.md#deployment) , а затем использовать его для получения URL-адресов других шаблонов в том же расположении. Это полезно, если расположение шаблонов меняется (например, при выходе новой версии) или если вы не хотите указывать URL-адреса непосредственно в файле шаблона. 

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="complete-example"></a>Полный пример
В следующих примерах шаблонов показано упрощенное размещение связанных шаблонов, чтобы проиллюстрировать некоторые основные понятия, используемые в этой статье. Предполагается, что шаблоны были добавлены в один контейнер в учетной записи хранения, для которой отключен общий доступ. Связанный шаблон передает значение обратно в основной шаблон в разделе **outputs** .

Содержимое файла **Parent.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.result.value]"
    }
  }
}
```

Содержимое файла **helloworld.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "result": {
        "value": "Hello World",
        "type" : "string"
    }
  }
}
```

В PowerShell происходит получение маркера для контейнера и развертывание шаблонов с помощью следующей команды.

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

В Azure CLI 2.0 осуществляется получение маркера для контейнера и развертывание шаблонов с помощью следующего кода.

```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о том, как определить порядок развертывания ресурсов, см. в статье [Определение зависимостей в шаблонах Azure Resource Manager](resource-group-define-dependencies.md).
* Сведения о том, как определить один ресурс и создать несколько экземпляров, см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

