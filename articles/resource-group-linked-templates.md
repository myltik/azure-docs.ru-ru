---
title: Связанные шаблоны в Resource Manager | Microsoft Docs
description: Описывает, как использовать связанные шаблоны в шаблоне диспетчера ресурсов Azure для создания решения модульных шаблонов. Показывает, как передавать значения параметров, указывать файл параметров и динамически создаваемые URL-адреса.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/02/2016
ms.author: tomfitz

---
# Использование связанных шаблонов в диспетчере ресурсов Azure
Один шаблон Azure Resource Manager можно связать с другим шаблоном, что позволяет разбивать развертывания на несколько целевых шаблонов с определенным назначением. Как и при разбивке приложения на несколько классов, декомпозиция развертывания гораздо удобнее в контексте тестирования, повторного использования и удобства чтения.

Можно передавать параметры из основного шаблона в связанный шаблон. Кроме того, эти параметры можно напрямую сопоставить с параметрами или переменными, предоставляемыми вызывающим шаблоном. Связанный шаблон может также передать выходную переменную в исходный шаблон, что позволяет активировать двусторонний обмен данными между шаблонами.

## Создание связи с шаблоном
Чтобы создать связь между двумя шаблонами, добавьте ресурс развертывания в основном шаблоне, который указывает на связанный шаблон. Задайте для свойства **templateLink** URI связанного шаблона. Значения параметров для связанного шаблона можно предоставить, указав значения непосредственно в шаблоне или привязав их к файлу параметров. В следующем примере используется свойство **parameters** для непосредственного указания значения параметра.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
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

Служба Resource Manager должна иметь доступ к связанному шаблону. В качестве связанного шаблона нельзя указать локальный файл или файл, доступный только в локальной сети. Можно предоставить только URI, который включает в себя **http** или **https**. Один из вариантов — разместить связанный шаблон в учетной записи хранения и использовать URI этого элемента, как показано в примере ниже.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Хотя связанный шаблон должны быть доступен извне, он не должен быть общедоступным. Шаблон можно добавить к личной учетной записи хранения, доступ к которой есть только у владельца учетной записи хранения. Затем создайте маркер подписанного URL-адреса для использования при развертывании. Этот маркер SAS добавляется в универсальный код ресурса (URI) связанного шаблона. Действия по настройке шаблона в учетной записи хранения и созданию маркера SAS описаны в разделах [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md) или [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](resource-group-template-deploy-cli.md).

В следующем примере показан родительский шаблон, связанный с другим шаблоном. Для доступа к связанному шаблону используется маркер SAS, который передается в качестве параметра.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
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

Несмотря на то, что маркер передается в защищенной строке, универсальный код ресурса (URI) связанного шаблона, включающий в себя маркер SAS, добавляется в журнал операций развертывания для данной группы ресурсов. Чтобы снизить риск раскрытия, задайте срок действия маркера.

## Создание связи с файлом параметров
В следующем примере используется свойство **parametersLink** привязки к файлу параметров.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
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

Значением универсального кода ресурса (URI) для связанного файла параметров не может быть локальный файл, оно должно содержать **http** или **https**. Доступ к файлу параметров также можно ограничить с помощью маркера SAS.

## Использование переменных для связывания шаблонов
В предыдущих примерах были показаны жестко запрограммированные значения URL-адреса для ссылок на шаблоны. Этот подход может подойти для простого шаблона, но он не действует при работе с большим набором модульных шаблонов. Вместо этого можно создать статическую переменную, которая содержит базовый URL-адрес для основного шаблона, а затем динамически создавать URL-адреса для связанных шаблонов на основе этого адреса. Преимущество этого подхода заключается в том, что можно легко переместить или разветвить шаблон, так как для этого необходимо лишь изменить статическую переменную в основном шаблоне. Основной шаблон передает правильные URI через разделенный шаблон.

В следующем примере показано, как использовать базовый URL-адрес для создания двух URL-адресов для связанных шаблонов (**sharedTemplateUrl** и **vmTemplate**).

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

Вы также можете получить базовый URL-адрес текущего шаблона с помощью функции [deployment()](resource-group-template-functions.md#deployment), а затем использовать его для получения URL-адресов других шаблонов в том же расположении. Это полезно, если расположение шаблонов меняется (например, при выходе новой версии) или если вы не хотите указывать URL-адреса непосредственно в файле шаблона.

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## Условное связывание шаблонов
Вы можете связывать разные шаблоны, передавая значение параметра, на основе которого создается URI связанного шаблона. Такой подход удобен, если во время развертывания нужно указать определенный связанный шаблон. Например, один шаблон может использоваться для работы с существующей учетной записью хранения, а другой — для создания новой учетной записи.

В следующем примере используются два параметра: один для имени учетной записи хранения и второй для определения используемой учетной записи (новая или существующая).

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Создайте переменную для URI шаблона, которая содержит значение нового или существующего параметра.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Укажите значение этой переменной для ресурса развертывания.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

Для URI определяется имя шаблона: **existingStorageAccount.json** или **newStorageAccount.json**. Создайте шаблоны для этих URI.

Вот пример шаблона **existingStorageAccount.json**.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

А ниже — пример шаблона **newStorageAccount.json**. Обратите внимание, что объект учетной записи хранения, как и в шаблоне существующей учетной записи хранения, возвращается в выходных данных. Основной шаблон поддерживает любой из связанных шаблонов.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## Полный пример
В следующих примерах шаблонов показано упрощенное размещение связанных шаблонов, чтобы проиллюстрировать некоторые основные понятия, используемые в этой статье. Предполагается, что шаблоны были добавлены в один контейнер в учетной записи хранения, для которой отключен общий доступ. Связанный шаблон передает значение обратно в основной шаблон в разделе **outputs**.

Содержимое файла **Parent.json**:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
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
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

Содержимое файла **helloworld.json**:

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

В PowerShell происходит получение маркера для контейнера и развертывание шаблонов с помощью следующей команды.

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

В интерфейсе командной строки Azure (Azure CLI) происходит получение маркера для контейнера и развертывание шаблонов с помощью следующего кода. В настоящее время необходимо указывать имя развертывания, если вы используете универсальный код ресурса (URI) шаблоны, который содержит маркер SAS.

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Вам будет предложено предоставить маркер SAS в качестве параметра. К маркеру нужно добавить префикс **?**.

## Дальнейшие действия
* Сведения о том, как определить порядок развертывания ресурсов, см. в статье [Определение зависимостей в шаблонах Azure Resource Manager](resource-group-define-dependencies.md).
* Сведения о том, как определить один ресурс и создать несколько экземпляров, см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

<!---HONumber=AcomDC_0907_2016-->