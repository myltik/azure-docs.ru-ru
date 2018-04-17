---
title: Связывание шаблонов для развертывания Azure | Документация Майкрософт
description: Описывает, как использовать связанные шаблоны в шаблоне диспетчера ресурсов Azure для создания решения модульных шаблонов. Показывает, как передавать значения параметров, указывать файл параметров и динамически создаваемые URL-адреса.
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
ms.date: 01/17/2018
ms.author: tomfitz
ms.openlocfilehash: 326d6873ae78c5f712832c4cfce9c793f1dfbf37
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Использование связанных и вложенных шаблонов при развертывании ресурсов Azure

Для развертывания решения можно использовать отдельный шаблон или основной шаблон с несколькими связанными шаблонами. Связанный шаблон может быть отдельным файлом, связанным с основным шаблоном или шаблоном, который вложен в основной шаблон.

Для небольших и средних решений отдельный шаблон проще в понимании и обслуживании. Все ресурсы и значения можно увидеть в отдельном файле. Для более сложных сценариев связанные шаблоны позволяют разбить решение на целевые компоненты и повторно использовать шаблоны.

При использовании связанного шаблона вы создаете основной шаблон, который получает значения параметра во время развертывания. Основной шаблон содержит все связанные шаблоны и при необходимости передает значения в эти шаблоны.

## <a name="link-or-nest-a-template"></a>Связывание или вложение шаблона

Чтобы создать связь между двумя шаблонами, добавьте ресурс **развертывания** в основной шаблон.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

Свойства, указанные для ресурса развертывания, зависят от того, создаете ли вы связь с внешним шаблоном или вкладываете встроенный шаблон в основной.

### <a name="nested-template"></a>Вложенный шаблон

Чтобы вложить шаблон в основной, используйте свойство **template** и укажите синтаксис шаблона.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> При вложении шаблона вы не можете использовать параметры или переменные, определенные во вложенном шаблоне. Можно использовать параметры и переменные из основного шаблона. В приведенном выше примере `[variables('storageName')]` извлекает значение из основного, а не из вложенного шаблона. Это ограничение не распространяется на внешние шаблоны.
>
> Вы не можете использовать функцию `reference` в разделе выходных данных вложенного шаблона. Чтобы извлечь значения для развернутого ресурса во вложенном шаблоне, преобразуйте этот шаблон в связанный.

### <a name="external-template-and-external-parameters"></a>Внешний шаблон и внешние параметры

Связать внешний шаблон и файл параметров можно с помощью свойств **templateLink** и **parametersLink**. При создании связи с шаблоном служба Resource Manager должна иметь к нему доступ. Вы не можете указать локальный файл или файл, доступный только в локальной сети. Можно предоставить только универсальный код ресурса (URI), который включает в себя **http** или **https**. Один из вариантов — разместить связанный шаблон в учетной записи хранения и использовать универсальный код ресурса (URI) этого элемента.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parametersLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

### <a name="external-template-and-inline-parameters"></a>Внешний шаблон и встроенные параметры

Вы также можете указать встроенный параметр. Передать значение из основного шаблона в связанный можно с помощью свойства **parameters**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

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

## <a name="get-values-from-linked-template"></a>Получение значений из связанного шаблона

Чтобы получить выходные значения из связанного шаблона, извлеките значение свойства с синтаксисом, например: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

В следующих примерах показано, как ссылаться на связанный шаблон и извлекать выходные значения. Связанный шаблон возвращает простое сообщение.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

Основной шаблон развертывает связанный шаблон и возвращает значение. Обратите внимание, что он ссылается на ресурс развертывания по имени и использует имя свойства, возвращенное связанным шаблоном.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Как и для прочих типов ресурсов, вы можете задать зависимости между связанным шаблоном и другими ресурсами. Следовательно, если другим ресурсам требуется выходное значение из связанного шаблона, то вы можете обеспечить развертывание данного шаблона прежде этих ресурсов. Или, если связанный шаблон зависит от других ресурсов, то вы можете обеспечить их развертывание перед развертыванием связанного шаблона.

Ниже приведен пример шаблона, который развертывает общедоступный IP-адрес и возвращает идентификатор ресурса.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Чтобы использовать общедоступный IP-адрес из предыдущего шаблона при развертывании подсистемы балансировки нагрузки, создайте связь с шаблоном и добавьте зависимость для ресурса развертывания. Для общедоступного IP-адреса в подсистеме балансировки нагрузки задается выходное значение из связанного шаблона.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Связанные и вложенные шаблоны в журнале развертывания

Resource Manager обрабатывает каждый шаблон как отдельное развертывание в журнале развертывания. Поэтому основной шаблон с тремя связанными и вложенными шаблонами отображается в журнале развертывания следующим образом.

![Журнал развертывания](./media/resource-group-linked-templates/deployment-history.png)

С помощью этих отдельных записей в журнале можно извлечь выходные значения после развертывания. Следующий шаблон создает общедоступный IP-адрес и выводит IP-адрес:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

Указанный ниже шаблон ссылается на предыдущий шаблон. Он создает три общедоступных IP-адреса.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
            }
        }
    ]
}
```

После развертывания можно извлечь выходные значения, выполнив следующий сценарий PowerShell.

```powershell
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Кроме того, можно выполнить сценарий Azure CLI.

```azurecli
for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Защита внешнего шаблона

Хотя связанный шаблон должны быть доступен извне, он не должен быть общедоступным. Шаблон можно добавить к личной учетной записи хранения, доступ к которой есть только у владельца учетной записи хранения. Затем создайте маркер подписанного URL-адреса для использования при развертывании. Этот маркер SAS добавляется в универсальный код ресурса (URI) связанного шаблона. Несмотря на то, что маркер передается в защищенной строке, универсальный код ресурса (URI) связанного шаблона, включающий в себя маркер SAS, добавляется в журнал операций развертывания. Чтобы снизить риск раскрытия, задайте срок действия маркера.

Доступ к файлу параметров также можно ограничить с помощью маркера SAS.

В следующем примере показано, как передать маркер SAS при создании связи с шаблоном.

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
  }
}
```

В PowerShell получение маркера для контейнера и развертывание шаблонов выполняется с помощью следующих команд. Обратите внимание на то, что параметр **containerSasToken** определен в шаблоне. Это не параметр команды **New-AzureRmResourceGroupDeployment**.

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

В Azure CLI происходит получение маркера для контейнера и развертывание шаблонов с помощью следующего кода.

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

## <a name="example-templates"></a>Образцы шаблонов

В следующих примерах показаны наиболее частые способы использования связанных шаблонов.

|Основной шаблон  |Связанный шаблон |ОПИСАНИЕ  |
|---------|---------| ---------|
|[Привет, мир!](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[связанный шаблон](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Возвращает строку из связанного шаблона. |
|[Подсистема балансировки нагрузки с общедоступным IP-адресом](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[связанный шаблон](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Возвращает общедоступный IP-адрес из связанного шаблона и задает это значение в подсистеме балансировки нагрузки. |
|[Несколько IP-адресов](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [связанный шаблон](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Создает несколько общедоступных IP-адресов в связанном шаблоне.  |

## <a name="next-steps"></a>Дополнительная информация

* Сведения о том, как определить порядок развертывания ресурсов, см. в статье [Определение порядка развертывания ресурсов в шаблонах Azure Resource Manager](resource-group-define-dependencies.md).
* Сведения о том, как определить один ресурс и создать несколько экземпляров, см. в статье [Развертывание нескольких экземпляров ресурса или свойства в шаблонах Azure Resource Manager](resource-group-create-multiple.md).
* Действия по настройке шаблона в учетной записи хранения и созданию маркера SAS описаны в разделах [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md) и [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](resource-group-template-deploy-cli.md).