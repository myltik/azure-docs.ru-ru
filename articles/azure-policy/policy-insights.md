---
title: Программное создание политик и просмотр данных о соответствии с использованием службы "Политика Azure"
description: В этой статье описано программное создание и управление политиками для службы "Политика Azure".
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 5405566b5254c553eac584acc1653449b51ddffc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195885"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Программное создание политик и просмотр данных о соответствии

В этой статье описано программное создание и управление политиками. В ней также описано, как просматривать состояния соответствия ресурсов и политики. Определения политик обеспечивают соблюдение различных правил и выполнение действий в ваших ресурсах. Принудительное применение обеспечивает соответствие ресурсов корпоративным стандартам и соглашениям об уровне обслуживания.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.

1. Установите [ARMClient](https://github.com/projectkudu/ARMClient), если его у вас еще нет. Это средство, которое отправляет HTTP-запросы к API-интерфейсам на основе Azure Resource Manager.
2. Обновите свой модуль AzureRM PowerShell до последней версии. Дополнительные сведения о последней версии см. по ссылке [для Azure PowerShell](https://github.com/Azure/azure-powershell/releases).
3. Зарегистрируйте поставщик ресурсов Policy Insights с помощью Azure PowerShell, чтобы связать подписку с поставщиком ресурсов. Чтобы зарегистрировать поставщик ресурсов, необходимо иметь разрешение на действие регистрации для поставщика ресурсов. Эта операция включается в роли участника и владельца. Выполните указанную ниже команду для регистрации поставщика ресурсов.

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Дополнительные сведения о регистрации и просмотре поставщиков ресурсов см. в статье [Поставщики и типы ресурсов](../azure-resource-manager/resource-manager-supported-services.md).
4. Установите интерфейс командной строки Azure CLI, если это еще не сделано. Последнюю версию CLI см. в статье [Установка Azure CLI 2.0 в Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Создание и назначение определения политики

Первый шаг для улучшения видимости ваших ресурсов — создать и назначить в них политики. Теперь необходимо научиться программно создавать и назначать политику. Пример политики проверяет учетные записи хранения, которые открыты для всех общедоступных сетей, используя запросы PowerShell, Azure CLI и HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Создание и назначение определения политики с помощью PowerShell

1. Используйте следующий фрагмент кода JSON, чтобы создать файл JSON с именем AuditStorageAccounts.json.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

  Дополнительные сведения о создании определения политики см. в статье [Структура определения службы "Политика Azure"](policy-definition.md).
2. Выполните следующую команду, чтобы создать определение политики с использованием файла AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  Эта команда создает определение политики с именем _Audit Storage Accounts Open to Public Networks_ (Аудит учетных записей хранения, открытых для общедоступных сетей). Дополнительные сведения о других параметрах, которые можно использовать, см. в статье о командлете [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. После создания определения политики вы можете создать назначение политики, выполнив следующие команды:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  Замените _ContosoRG_ именем вашей целевой группы ресурсов.

Дополнительные сведения об управлении политиками ресурсов с помощью модуля PowerShell Azure Resource Manager см. в разделе [Политики](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Создание и назначение определения политики с помощью ARMClient

Выполните указанные ниже действия, чтобы создать определение политики.

1. Скопируйте следующий фрагмент кода JSON, чтобы создать файл JSON. Вызовите файл на следующем шаге.

  ```json
  "properties": {
      "displayName": "Audit Storage Accounts Open to Public Networks",
      "policyType": "Custom",
      "mode": "Indexed",
      "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
      "parameters": {},
      "policyRule": {
          "if": {
              "allOf": [{
                      "field": "type",
                      "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                      "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                      "equals": "Allow"
                  }
              ]
          },
          "then": {
              "effect": "audit"
          }
      }
  }
  ```

2. Создайте определение политики с использованием следующего вызова:

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Замените предыдущий &lt;идентификатор подписки&gt; идентификатором требуемой подписки.

Дополнительные сведения о структуре запроса см. в разделе [Создание и обновление определений политик](/rest/api/resources/policydefinitions/createorupdate) (Policy Definitions - Create Or Update).

Используйте следующую процедуру для создания назначения политики и назначьте определение политики на уровне группы ресурсов.

1. Скопируйте следующий фрагмент кода JSON, чтобы создать файл JSON назначения политики. Замените данные в символах &lt;&gt; на собственные значения.

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      }
  }
  ```

2. Создайте назначение политики с использованием следующего вызова:

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Замените данные в символах &lt;&gt; на собственные значения.

  Дополнительные сведения о том, как выполнять HTTP-запросы к REST API, см. в статье [Azure Resource Manager](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Создание и назначение определения политики с помощью Azure CLI

Чтобы создать определение политики, выполните указанные ниже действия.

1. Скопируйте следующий фрагмент кода JSON, чтобы создать файл JSON назначения политики.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

2. Чтобы создать определение политики, выполните следующую команду:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Чтобы создать назначение политики, выполните следующую команду: Замените данные в символах &lt;&gt; на собственные значения.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

Вы можете получить идентификатор определения политики с помощью следующей команды в PowerShell.

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Идентификатор определения политики для созданного вами определения должен выглядеть следующим образом:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Дополнительные сведения об управлении политиками ресурсов с помощью Azure CLI см. в [этой статье](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Выявление несоответствующих ресурсов

В назначении ресурс не соответствует требованиям, если он не соответствует правилам политики или инициативы. В следующей таблице показано, как действуют разные политики в сочетании с оценкой условий для определения итогового состояния соответствия.

| Состояние ресурса | Результат | Оценка политики | Состояние соответствия |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Истина | Не соответствует |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Ложь | Соответствует |
| Создать | Audit, AuditIfNotExist\* | Истина | Не соответствует |
| Создать | Audit, AuditIfNotExist\* | Ложь | Соответствует |

\*Для эффектов Append, DeployIfNotExist и AuditIfNotExist требуется, чтобы оператор IF имел значение TRUE. Эффекты также требуют, чтобы условие существования FALSE было несоответствующим. Когда установлено значение TRUE, условие IF запускает оценку условия существования для связанных ресурсов.

Чтобы лучше понять, как ресурсы помечаются как несоответствующие требованиям, используйте пример назначения политики, созданный выше.

Например, предположим, что у вас есть группа ресурсов ContosoRG с некоторыми учетными записями хранения (выделены красным цветом), которые доступны в общедоступных сетях.

![Учетные записи хранения, доступные в общедоступных сетях](media/policy-insights/resource-group01.png)

В этом примере необходимо опасаться угроз безопасности. Теперь, когда вы создали назначение политики, оно оценивается для всех учетных записей хранения в группе ресурсов ContosoRG. Оно проверяет три несовместимые учетные записи хранения, соответственно меняя их состояние на**не соответствующее**.

![Прошедшие аудит несоответствующие учетные записи хранения](media/policy-insights/resource-group03.png)

Используйте приведенную ниже процедуру для идентификации ресурсов в группе ресурсов, которые не соответствуют назначению политики. В этом примере ресурсы — это учетные записи хранения в группе ресурсов ContosoRG.

1. Получите идентификатор назначения политики, выполнив следующие команды.

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks' }
  $policyAssignment.PolicyAssignmentId
  ```

  Дополнительные сведения о получении идентификаторов назначения политики см. в статье о команде [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment).

2. Выполните следующую команду, чтобы скопировать идентификаторы несовместимых ресурсов в JSON-файл:

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. Результат должен выглядеть следующим образом:

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

Результаты похожи на содержимое списка **несовместимых ресурсов** в [представлении портала Azure](assign-policy-definition.md#identify-non-compliant-resources).

В настоящее время несоответствующие ресурсы идентифицируются только с использованием портала Azure и HTTP-запросов. Дополнительные сведения о запросах состояний политик см. в справочной статье по API [Policy State](/rest/api/policy-insights/policystates) (Состояние политики).

## <a name="view-policy-events"></a>Просмотр событий политики

После создания или обновления ресурса создается результат оценки политики. Такие результаты называются _событиями политики_. Выполните следующий запрос, чтобы просмотреть все события политики, связанные с назначением политики.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

Результаты должны выглядеть примерно так:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

Как и в случае с состояниями политики, с помощью HTTP-запроса вы можете только просматривать события политики. Дополнительные сведения о запросах событий политик см. в справочной статье по [Policy Events](/rest/api/policy-insights/policyevents) (События политик).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о командах и запросах, используемых в этой статье, см. в следующих ресурсах.

- [Ресурсы REST API Azure](/rest/api/resources/)
- [Модули Azure RM PowerShell](/powershell/module/azurerm.resources/#policies)
- [Команды Azure CLI для роботы с политикой](/cli/azure/policy?view=azure-cli-latest)
- [Policy Insights](/rest/api/policy-insights)