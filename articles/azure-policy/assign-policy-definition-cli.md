---
title: Создание назначения политики с помощью Azure CLI для идентификации ресурсов, не соответствующих требованиям, в среде Azure | Документация Майкрософт
description: Использование PowerShell для создания назначения политики Azure для идентификации несоответствующих ресурсов.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 1ff1240073e25bf406e7da6b79135264376a5b3f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Создание назначения политики для идентификации ресурсов, не соответствующих требованиям, в среде Azure с помощью Azure CLI | Документация Майкрософт

Чтобы понять, соответствуют ли ресурсы требованиям в Azure, прежде всего нужно определить их состояние. В этом кратком руководстве описано, как создать назначение политики для определения виртуальных машин, которые не используют управляемые диски.

Завершив работу, вы узнаете, какие виртуальные машины не используют управляемые диски, так как *не соответствуют* назначению политики.

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью сценариев. В этом руководстве используется Azure CLI для создания назначения политики для определения в среде Azure ресурсов, не соответствующих требованиям.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Для этого руководства вам потребуется Azure CLI 2.0.4 или более поздней версии, чтобы установить и использовать интерфейс командной строки локально. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).



## <a name="create-a-policy-assignment"></a>Создание назначения политики

В этом кратком руководстве вы создадите назначение политики и назначите определение "Audit Virtual Machines without Managed Disks". Это определение политики идентифицирует ресурсы, которые не соответствуют заданным в нем условиям.

Чтобы создать назначение политики, выполните следующую команду:

```
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

В указанной выше команде используются следующие сведения:

- **Name** — отображаемое имя назначения политики. В этом случае используйте определение *Audit Virtual Machines without Managed Disks*.
- **Policy** — идентификатор определения политики, на основе которой вы создаете назначение. В нашем случае это определение политики *Audit Virtual Machines without Managed Disks*. Чтобы получить идентификатор определения политики, выполните следующую команду: `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **Scope.** Область определяет, к каким ресурсам или группе ресурсов принудительно применяется назначение политики. Политика может назначаться разным ресурсам: от подписки до групп ресурсов. Обязательно замените значение &lt;scope&gt; именем своей группы ресурсов.
- **Sku.** При помощи этой команды создается назначение политики с уровнем "Стандартный". Уровень "Стандартный" позволяет управлять масштабированием, оценивать соответствие и вносить требуемые исправления. Подробнее о ценовых категориях см. на странице [с ценами на использование Политики Azure](https://azure.microsoft.com/pricing/details/azure-policy).


## <a name="identify-non-compliant-resources"></a>Выявление несоответствующих ресурсов

Чтобы просмотреть ресурсы, которые не соответствуют новому назначению, получите идентификатор назначения политики, выполнив следующие команды:

```
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```
$policyAssignment.PolicyAssignmentId
```

Дополнительные сведения об идентификаторах назначения политики см. в описании командлета [Get AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Теперь выполните следующую команду, чтобы вывести идентификаторы несовместимых ресурсов в JSON-файле:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Результаты должны выглядеть примерно так:

```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
    "@odata.id": null,
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
    },
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
         },
{
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
         }

]
}

```

Результаты похожи на содержимое списка **несовместимых ресурсов** в представлении портала Azure.

## <a name="clean-up-resources"></a>Очистка ресурсов

Остальные руководства из этой серии являются продолжением этого документа. Если вы собираетесь продолжать работу с ними, не удаляйте ресурсы, которые вы создали при работе с этим руководством. В противном случае удалите созданное назначение, выполнив следующую команду:

```azurecli
az policy assignment delete –name Audit Virtual Machines without Managed Disks Assignment --scope /subscriptions/ <subscriptionID> / <resourceGroupName>
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы назначили определение политики для идентификации ресурсов, не соответствующих требованиям, в среде Azure.

Дополнительные сведения о назначении политик и обеспечении соответствия создаваемых в **будущем** ресурсов см. в следующем руководстве:

> [!div class="nextstepaction"]
> [Создание политик и управление ими](./create-manage-policy.md)
