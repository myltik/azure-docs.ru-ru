---
title: Запрещение изменений в ресурсах Azure | Документация Майкрософт
description: Запрет на обновление или удаление критических ресурсов Azure для пользователей путем применения блокировки для всех пользователей и ролей.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 1a0f813e1857d2f1c1cc36b34b6339d26fa91c13
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602692"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Блокировка ресурсов для предотвращения непредвиденных изменений 

Администратору иногда требуется заблокировать подписку, ресурс или группу ресурсов, чтобы другие пользователи в организации не могли случайно удалить или изменить критически важные ресурсы. Можно установить уровень блокировки **CanNotDelete** или **ReadOnly**. На портале блокировки называются **Удаление** и **Только для чтения**, соответственно.

* **CanNotDelete** означает, что авторизованные пользователи смогут читать и изменять ресурс, но не смогут его удалить. 
* **ReadOnly** означает, что авторизованные пользователи смогут читать ресурс, но не смогут его удалить или обновить. Применение этой блокировки подобно ограничению авторизованных пользователей с помощью разрешений, предоставляемых для роли **Читатель**. 

## <a name="how-locks-are-applied"></a>Применение блокировок

При применении блокировки к родительской области все ресурсы в этой области наследуют ту же блокировку. Даже ресурсы, добавляемые позже, наследуют блокировку от родительского ресурса. Приоритет имеет наиболее строгая блокировка.

В отличие от управления доступом на основе ролей блокировки управления используются для применения ограничения для всех пользователей и ролей. Сведения о настройке разрешений для пользователей и ролей см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../role-based-access-control/role-assignments-portal.md).

Блокировки Resource Manager применяются только к операциям, выполняемым в плоскости управления, которая включает в себя операции, передаваемые на `https://management.azure.com`. Эти блокировки не мешают ресурсам осуществлять свои собственные функции. Ограничиваются изменения ресурсов, но не операции с ними. Например, блокировка ReadOnly в Базе данных SQL не позволит вам удалить или изменить базу данных, но она не мешает создавать, обновлять и удалять данные в базе данных. Транзакции с данными разрешены, так как эти операции не передаются на `https://management.azure.com`.

Применение уровня блокировки **ReadOnly** (Только чтение) может привести к непредвиденным результатам, так как некоторые операции, кажущиеся операциями чтения, на самом деле требуют дополнительных действий. Например, установка уровня блокировки **ReadOnly** для учетной записи хранения не позволит всем пользователям получить список ключей. Операция отображения списка ключей обрабатывается с помощью запроса POST, поскольку возвращаемые ключи могут быть доступны для операций записи. Еще один пример: установка уровня блокировки **ReadOnly** для ресурса службы приложений не позволит обозревателю сервера Visual Studio отображать файлы для ресурса, так как для их взаимодействия требуется доступ на запись.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Кто может создавать и удалять блокировки в вашей организации
Для создания или удаления блокировок управления необходим доступ к действию `Microsoft.Authorization/*` или `Microsoft.Authorization/locks/*`. Из встроенных ролей эти действия предоставляются только **владельцу** и **администратору доступа пользователей**.

## <a name="portal"></a>Microsoft Azure
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Шаблон
В следующем примере показан шаблон, который позволяет создать план обслуживания приложений, веб-сайт и блокировку на веб-сайте. Блокировка — это тип ресурса для блокировки и **/providers/locks**. Имя блокировки состоит из имени ресурса, суффикса **/Microsoft.Authorization/** и имени самой блокировки.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroup -Name sitegroup -Location southcentralus
New-AzureRmResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

## <a name="powershell"></a>PowerShell
Вы можете заблокировать развернутые ресурсы с помощью Azure PowerShell, выполнив команду [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock).

Чтобы заблокировать ресурс, укажите имя и тип ресурса, а также имя группы ресурсов.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Чтобы заблокировать группу ресурсов, укажите ее имя.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Чтобы получить сведения о блокировке, выполните команду [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Чтобы получить все блокировки в подписке, выполните следующую команду:

```powershell
Get-AzureRmResourceLock
```

Чтобы получить все блокировки для ресурса, выполните следующую команду:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Чтобы получить все блокировки для группы ресурсов, выполните следующую команду:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Чтобы удалить блокировку, используйте эту команду:

```powershell
$lockId = (Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzureRmResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Вы можете заблокировать развернутые ресурсы с помощью Azure CLI, выполнив команду [az lock create](/cli/azure/lock#az_lock_create).

Чтобы заблокировать ресурс, укажите имя и тип ресурса, а также имя группы ресурсов.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Чтобы заблокировать группу ресурсов, укажите ее имя.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Чтобы получить сведения о блокировке, выполните команду [az lock list](/cli/azure/lock#az_lock_list). Чтобы получить все блокировки в подписке, выполните следующую команду:

```azurecli
az lock list
```

Чтобы получить все блокировки для ресурса, выполните следующую команду:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Чтобы получить все блокировки для группы ресурсов, выполните следующую команду:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Чтобы удалить блокировку, используйте эту команду:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>ИНТЕРФЕЙС REST API
Вы можете заблокировать развернутые ресурсы с помощью [REST API для блокировок управления](https://docs.microsoft.com/rest/api/resources/managementlocks). REST API позволяет создавать и удалять блокировки и получать информацию о существующих блокировках.

Чтобы создать блокировку, выполните следующую команду:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Областью может быть подписка, группа ресурсов или ресурс. Вы можете назначить блокировке любое имя. В качестве версии API (api-version) используйте значение **2015-01-01**.

В запросе включите объект JSON, который задает свойства блокировки.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о логической организации ресурсов см. в статье [Использование тегов для организации ресурсов](resource-group-using-tags.md).
* Изменение группы, в которой находится ресурс, описано в статье [Перемещение ресурсов в новую группу ресурсов](resource-group-move-resources.md).
* Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. Дополнительные сведения см. в статье [Что такое служба "Политика Azure"](../azure-policy/azure-policy-introduction.md).
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).

