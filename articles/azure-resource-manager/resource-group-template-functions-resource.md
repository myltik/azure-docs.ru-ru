---
title: Функции шаблонов Azure Resource Manager для работы с ресурсами | Документация Майкрософт
description: Описывает функции, используемые в шаблоне Azure Resource Manager для получения значений ресурсов.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: tomfitz
ms.openlocfilehash: 9ba4c9d9cd5f8a43be0f97053c02798e3b84a5f7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Функции для работы с ресурсами в шаблонах Azure Resource Manager

Диспетчер ресурсов предоставляет следующие функции для получения значений ресурсов:

* [listKeys](#listkeys)
* [listSecrets](#list)
* [list*](#list)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)

Получение значений параметров, переменных или текущего развертывания описано в разделе [Функции для параметров развертывания](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-listsecrets-and-list"></a>listKeys, listSecrets и list*
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`listSecrets(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Возвращает значения для любого типа ресурса, который поддерживает операцию list. Наиболее распространенными вариантами применения являются `listKeys` и `listSecrets`. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| имя_ресурса или идентификатор_ресурса |Yes |строка |Уникальный идентификатор ресурса. |
| версия_API |Yes |строка |Версия API для состояния среды выполнения ресурса. Как правило, указывается в формате **гггг-мм-дд**. |

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый функцией listKeys объект имеет следующий формат:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Другие функции list возвращают данные в других форматах. Чтобы просмотреть формат функции, включите ее в раздел outputs, как показано в примере шаблона. 

### <a name="remarks"></a>Примечания

Любая операция, которая начинается с **list**, может быть использована в шаблоне как функция. Это относится не только к listKeys, но и к таким операциям, как `list`, `listAdminKeys` и `listStatus`. Но нельзя использовать операции **list**, требующие значения в тексте запроса. Например, для операции [перечисления SAS учетной записи](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) требуются параметры текста запроса, такие как *signedExpiry*, поэтому ее нельзя использовать внутри шаблона.

Чтобы определить, какие типы ресурсов поддерживают операцию list, можно использовать следующие варианты:

* Просмотрите [операции REST API](/rest/api/) для поставщика ресурсов и найдите операции list. Например, в учетных записях хранения есть [операция listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Воспользуйтесь командлетом PowerShell [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). В следующем примере извлекаются все операции list для учетных записей хранения:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Используйте следующую команду Azure CLI, чтобы отфильтровать только операции list:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

Укажите ресурс с помощью [функции resourceId](#resourceid) или формата `{providerNamespace}/{resourceType}/{resourceName}`.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) показано, как получить в разделе выходных данных первичный и вторичный ключи из учетной записи хранения.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[listKeys(parameters('storageAccountName'), '2016-12-01')]"
      }
    }
}
``` 

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json --parameters storageAccountName=<your-storage-account>
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json -storageAccountName <your-storage-account>
```

<a id="providers" />

## <a name="providers"></a>providers
`providers(providerNamespace, [resourceType])`

Возвращает сведения о поставщике ресурсов и поддерживаемых типах ресурсов. Если тип ресурса не указан, функция возвращает все типы, поддерживаемые для поставщика ресурсов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| пространство_имен_поставщика |Yes |строка |Пространство имен поставщика. |
| тип_ресурса |Нет  |строка |Тип ресурса в указанном пространстве имен. |

### <a name="return-value"></a>Возвращаемое значение

Все поддерживаемые типы возвращаются в следующем формате: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Упорядочение массива возвращаемых значений не гарантируется.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) показано, как использовать функцию provider:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Для поставщика ресурсов **Microsoft.Web** и типа ресурса **sites** приведенный выше пример возвращает объект в таком формате:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json --parameters providerNamespace=Microsoft.Web resourceType=sites
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json -providerNamespace Microsoft.Web -resourceType sites
```

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Возвращает объект, представляющий состояние среды выполнения ресурса.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| имя_ресурса или идентификатор_ресурса |Yes |строка |Имя или уникальный идентификатор ресурса. |
| версия_API |Нет  |строка |Версия API для указанного ресурса. Если ресурс не предоставляется в рамках того же шаблона, необходимо включить этот параметр. Как правило, указывается в формате **гггг-мм-дд**. |
| Full |Нет  |строка |Значение, указывающее, следует ли возвращать полный объект ресурса. Если вы не укажете `'Full'`, возвращается только объект свойств ресурса. Полный объект включает такие значения, как идентификатор ресурса и расположение. |

### <a name="return-value"></a>Возвращаемое значение

Каждый тип ресурса возвращает для функции reference разные свойства. Функция не возвращает данные в едином предварительно заданном формате. Кроме того, возвращаемое значение зависит от того, указали ли вы полный объект. Чтобы просмотреть свойства для типа ресурса, возвратите объект в разделе outputs, как показано в примере.

### <a name="remarks"></a>Примечания

Функция reference получает свое значение из состояния среды выполнения, и поэтому ее невозможно использовать в разделе переменных. Она может использоваться в разделе выходных данных шаблона или [связанного шаблона](resource-group-linked-templates.md#link-or-nest-a-template). Вы не можете использовать эту функцию в разделе выходных данных [вложенного шаблона](resource-group-linked-templates.md#link-or-nest-a-template). Чтобы извлечь значения для развернутого ресурса во вложенном шаблоне, преобразуйте этот шаблон в связанный. 

С помощью функции reference вы прямо объявляете, что один ресурс зависит от другого, если ресурс, на который указывает ссылка, предоставляется в том же шаблоне. При этом свойство dependsOn использовать не нужно. Расчет функции выполняется только после развертывания ресурса, на который указывает ссылка.

Чтобы просмотреть имена и значения свойств для типа ресурса, создайте в разделе outputs шаблон, который возвращает объект. Если ресурс этого типа уже существует, то шаблон возвращает объект, не развертывая новых ресурсов. 

Обычно функция **reference** используется, чтобы получить определенное значение из объекта, например универсальный код ресурса (URI) конечной точки большого двоичного объекта или полное доменное имя.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Используйте `'Full'`, если вам нужны значения ресурсов, которые не являются частью схемы свойств. Например, чтобы задать политику доступа к хранилищу ключей, получите свойства идентификатора для виртуальной машины.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

Полный пример предыдущего шаблона см. в репозитории [Windows to Key Vault](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json). Аналогичный пример доступен для [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json).

### <a name="example"></a>Пример

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) развертывает ресурс и ссылается на этот ресурс.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

В предыдущем примере возвращаются два объекта. Объект свойств имеет следующий формат:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Полный объект имеет следующий формат:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json --parameters storageAccountName=<your-storage-account>
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json -storageAccountName <your-storage-account>
```

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) ссылается на учетную запись хранения, которая не развертывается в этом шаблоне. Учетная запись хранения уже существует в той же группе ресурсов.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json --parameters storageAccountName=<your-storage-account>
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json -storageAccountName <your-storage-account>
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Возвращает объект, который представляет текущую группу ресурсов. 

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект имеет следующий формат:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>Примечания

Как правило, функция resourceGroup используется для создания ресурсов в одном расположении с группой ресурсов. В следующем примере расположение группы ресурсов используется для назначения расположения веб-сайту.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Пример

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) возвращает свойства группы ресурсов.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

В предыдущем примере возвращается объект в следующем формате:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json 
```

<a id="resourceid" />

## <a name="resourceid"></a>ResourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Возвращает уникальный идентификатор ресурса. Используйте эту функцию в том случае, когда имя ресурса является неоднозначным или не было предоставлено в пределах того же шаблона. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| subscriptionId |Нет  |строка (в формате GUID) |Значение по умолчанию — текущая подписка. Укажите это значение, если нужно получить ресурс из другой подписки. |
| имя_группы_ресурсов |Нет  |строка |Значение по умолчанию — текущая группа ресурсов. Укажите это значение, если нужно получить ресурс из другой группы ресурсов. |
| тип_ресурса |Yes |строка |Тип ресурса, включая пространство имен поставщика ресурсов. |
| имя_ресурса1 |Yes |строка |Имя ресурса. |
| имя_ресурса2 |Нет  |строка |Имя следующего ресурса, если ресурс является вложенным. |

### <a name="return-value"></a>Возвращаемое значение

Идентификатор возвращается в следующем формате:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Примечания

Указанные значения параметров зависят от того, находится ли ресурс в той же подписке и группе ресурсов, что и текущее развертывание.

Чтобы получить идентификатор ресурса для учетной записи хранения в одной подписке и группе ресурсов, используйте команду ниже:

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Чтобы получить идентификатор ресурса для учетной записи хранения в той же подписке, но другой группе ресурсов, используйте команду ниже:

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Чтобы получить идентификатор ресурса для учетной записи хранения в других подписке и группе ресурсов, используйте команду ниже:

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Чтобы получить идентификатор ресурса для базы данных в другой группе ресурсов, используйте команду ниже:

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Эта функция часто необходима при использовании учетной записи хранения или виртуальной сети в альтернативной группе ресурсов. В следующем примере показано, как ресурс из внешней группы ресурсов можно легко использовать:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>Пример

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) возвращает идентификатор ресурса для учетной записи хранения в группе ресурсов:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| sameRGOutput | Строка | /subscriptions/{ИД_текущей_подписки}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Строка | /subscriptions/{ИД_текущей_подписки}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Строка | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Строка | /subscriptions/{ИД_текущей_подписки}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json 
```

<a id="subscription" />

## <a name="subscription"></a>Подписка
`subscription()`

Возвращает сведения о подписке для текущего развертывания. 

### <a name="return-value"></a>Возвращаемое значение

Функция возвращает значение в следующем формате:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) показана функция subscription, вызываемая в разделе выходных данных. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json 
```

## <a name="next-steps"></a>Дополнительная информация
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).

