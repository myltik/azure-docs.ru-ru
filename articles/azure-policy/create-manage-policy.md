---
title: "Использование Политики Azure для создания политик и управления ими, чтобы обеспечить соответствие требованиям организации | Документация Майкрософт"
description: "Использование Политики Azure для обеспечения соблюдения стандартов, соответствия нормам и требованиям аудита, контроля затрат, обеспечения безопасности и согласованности производительности, а также установки корпоративных принципов проектирования."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 11/01/2017
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: adbf6e13efaad196c39e4fce0900fa40d7511122
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Создание политик и управление ими для обеспечения соответствия требованиям

Понимание того, как создавать политики и управлять ими в Azure, важно для обеспечения соответствия корпоративным стандартам и соглашениям об уровне обслуживания. В этом руководстве вы научитесь использовать Политику Azure для выполнения некоторых общих задач, связанных с созданием, назначением и управлением политиками в вашей организации, таких как:

> [!div class="checklist"]
> * Назначение политики для применения условий для ресурсов, которые вы создадите в будущем.
> * Создание и назначение определения инициативы для отслеживания соответствия нескольких ресурсов.
> * Обход запрета на создание несоответствующих или отклоненных ресурсов.
> * Внедрение новой политики в организации.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="opt-in-to-azure-policy"></a>Регистрация в службе "Политика Azure"

Служба "Политика Azure" сейчас доступна в ограниченной предварительной версии, поэтому вам необходимо зарегистрироваться, чтобы подать запрос на доступ.

1. Перейдите к службе "Политика Azure" по адресу https://aka.ms/getpolicy, а затем выберите **Зарегистрироваться** в области слева.

   ![Поиск политики](media/assign-policy-definition/sign-up.png)

2. Согласитесь использовать политику Azure, выбрав подписки, с которыми желаете работать, в списке **Подписка**. Затем щелкните **Регистрация**.

   Список подписок включает все подписки Azure.

   ![Регистрация для использования Политики Azure](media/assign-policy-definition/preview-opt-in.png)

   Подтверждение запроса на регистрацию может занять несколько дней, в зависимости от числа запросов. После принятия запроса по электронной почте вам будет отправлено сообщение о том, что можно начинать использование службы.

## <a name="assign-a-policy"></a>Назначение политики

Первым шагом для обеспечения соответствия Политике Azure является назначение определения политики. Определение политики определяет, при каких условиях применяется политика и какое действие следует предпринять. В этом примере мы назначаем встроенное определение политики, называемое *Require SQL Server Version 12.0* (Требовать наличия SQL Server версии 12.0), чтобы обеспечить соответствие всех баз данных SQL Server версии 12.0.

1. Запустите службу "Политика Azure" на портале Azure, выполнив поиск и выбрав **Политика** в левой области.

   ![Поиск политики](media/assign-policy-definition/search-policy.png)

2. Выберите **Назначения** на левой панели страницы Политики Azure. Назначение — это политика, которая назначена в рамках определенной области.
3. Выберите **Назначить политику** в верхней части панели **Назначения**.

   ![Назначение определения политики](media/create-manage-policy/select-assign-policy.png)

4. На странице **Назначить политику** нажмите ![кнопку определения политики](media/assign-policy-definition/definitions-button.png) рядом с полем **Политика**, чтобы открыть список доступных определений.

   ![Список доступных определений политик](media/create-manage-policy/open-policy-definitions.png)

5. Выберите **Require SQL Server Version 12.0** (Требовать наличия SQL Server версии 12.0).

   ![Поиск политики](media/create-manage-policy/select-available-definition.png)

6. Введите отображаемое **имя** для назначения политики. Здесь мы используем имя *Require SQL Server version 12.0* (Требовать наличия SQL Server версии 12.0). При желании вы можете добавить необязательное **описание**. Описание предоставляет сведения о том, как назначение этой политики проверяет все создаваемые в среде серверы SQL Server на наличие версии 12.0.
7. Установите ценовую категорию **Стандартный**, чтобы политика применялась и к существующим ресурсам.

   В Политике Azure используются две ценовые категории: *Бесплатный* и *Стандартный*. Бесплатная категория позволяет применять политики только к новым ресурсам, а стандартная распространяет действие политик и на существующие ресурсы, что поможет вам лучше понять состояние соответствия. Пока мы используем режим ограниченной предварительной версии, модель ценообразования еще не готова. Это означает, что вам пока не нужно оплачивать категорию *Стандартный*. Подробности о ценах можно узнать на [странице ценообразования для Политики Azure](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/).

8. Выберите **область** — подписку (или группу ресурсов), которую вы ранее зарегистрировали для использования с Политикой Azure. Она определяет, к каким ресурсам или группе ресурсов принудительно применяется назначение политики. Она может варьировать от подписки до групп ресурсов.

   В этом примере мы используем подписку **Azure Analytics Capacity Dev**. Ваша подписка будет отличаться.

10. Выберите **Назначить**.

## <a name="implement-a-new-custom-policy"></a>Реализация новой пользовательской политики

Теперь, когда мы назначили определение политики, создадим новую политику для экономии затрат. Эта политика гарантирует, что в вашей среде не будут созданы виртуальные машины серии G. Таким образом, каждый раз, когда пользователь в вашей организации пытается создать виртуальную машину серии G, запрос будет отклонен.

1. Выберите **Определение** на вкладке **Разработка** в левой области.

   ![Определение на вкладке разработки](media/create-manage-policy/definition-under-authoring.png)

2. Выберите **+ Определение политики**.
3. Заполните следующие поля:

   - Имя определения политики: *Require VM SKUs smaller than the G series* (Требовать номер SKU виртуальной машины меньше, чем серия G).
   - Описание того, для чего предназначено определение политики. Это определение политики гарантирует, что все виртуальные машины, созданные в этой области, будут иметь номера SKU меньше, чем серия G, что позволяет сэкономить затраты.
   - Подписка, в которой будет находиться определение политики. В этом случае определение политики будет находиться в подписке **Advisor Analytics Capacity Dev**. Ваш список подписок будет отличаться.
   - Напишите код JSON, который будет содержать:
      - параметры политики;
      - правила и условия политики — в данном случае номер SKU виртуальной машины, соответствующий серии G;
      - действие политики — в этом случае **Отменить**.

   Код JSON должен выглядеть следующим образом:

```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
```

<!-- Update the following link to the top level samples page
-->
   Образцы кода JSON см. в статье [Общие сведения о политике ресурсов](../azure-resource-manager/resource-manager-policy.md).

4. Щелкните **Сохранить**.

## <a name="create-a-policy-definition-with-rest-api"></a>Создание определения политики с использованием REST API

Для создания политики вы можете использовать REST API для определений политик. API REST позволяет создавать и удалять определения политик и получать сведения о существующих определениях.
Чтобы создать определение политики, используйте следующий пример.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Добавьте текст запроса. Ниже приведен соответствующий пример.

```
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Создание определения политики с помощью PowerShell

Прежде чем продолжить работу с примером PowerShell, убедитесь, что у вас установлена последняя версия Azure PowerShell. Параметры политики были добавлены в версии 3.6.0. Если установлена более ранняя версия, примеры возвращают ошибку из-за того, что параметр не найден.

Определение политики можно создать с помощью командлета `New-AzureRmPolicyDefinition`.

Чтобы создать определение политики из файла, передайте путь в файл. Для внешнего файла используйте следующий пример.

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Для применения локального файла используйте следующий пример.

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Для создания определения политики с помощью встроенного правила используйте следующий пример.

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

Выходные данные сохраняются в объекте `$definition`, который используется при назначении политики.
В следующем примере создается определение политики, которое включает параметры:

```
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Просмотр определений политик

Чтобы просмотреть все определения политик в подписке, используйте приведенную ниже команду.

```
Get-AzureRmPolicyDefinition
```

Она возвращает все доступные определения политик, включая встроенные политики. Каждая политика возвращается в приведенном ниже формате.

```
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Создание определения политики с использованием Azure CLI

Определение политики можно создать с помощью Azure CLI, выполнив в нем команду определения политики.
Для создания определения политики с помощью встроенного правила используйте следующий пример.

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Просмотр определений политик

Чтобы просмотреть все определения политик в подписке, используйте приведенную ниже команду.

```
az policy definition list
```

Она возвращает все доступные определения политик, включая встроенные политики. Каждая политика возвращается в приведенном ниже формате.

```
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Создание определения инициативы и его назначение

С помощью определения инициативы вы можете сгруппировать несколько определений политики для достижения одной ключевой цели. Определение инициативы создается, чтобы гарантировать, что ресурсы в рамках определения будут соответствовать определениям политики, которые составляют определение инициативы.  Дополнительные сведения об определениях инициативы см. в статье [Что такое служба "Политика Azure"?](./azure-policy-introduction.md).

### <a name="create-an-initiative-definition"></a>Создание определения инициативы

1. Выберите **Определения** на вкладке **Разработка** в левой области.

   ![Выбор определений](media/create-manage-policy/select-definitions.png)

2. Выберите **Initiative Definition** (Определение инициативы) в верхней части. Отобразится форма **Initiative Definition** (Определение инициативы).
3. Введите имя и описание инициативы.

   В этом примере мы хотим, чтобы ресурсы соответствовали определениям политик, связанным с безопасностью. Название инициативы — **Защита**, а описание — **Эта инициатива была создана для обработки всех определений политик, связанных с защитой ресурсов**.

   ![Определение инициативы](media/create-manage-policy/initiative-definition.png)

4. Просмотрите список **доступных определений** и выберите определения политики, которые вы хотите добавить к этой инициативе. Для инициативы **Защита** добавьте следующие встроенные определения политик:
   - Require SQL Server version 12.0;
   - Monitor unprotected web applications in the security center (Отслеживать незащищенные веб-приложения в центре безопасности);
   - Monitor permissive network across in Security Center (Отслеживать разрешенную сеть в центре безопасности);
   - Monitor possible app Whitelisting in Security Center (Отслеживать список разрешенных приложений в центре безопасности);
   - Monitor unencrypted VM Disks in Security Center (Отслеживать незашифрованные диски виртуальных машин в центре безопасности).

   ![Определения инициативы](media/create-manage-policy/initiative-definition-2.png)

   Как только вы выберите определения политики в списке, они появятся в разделе **Policies and parameters** (Политики и параметры), как показано выше.

5. Нажмите кнопку **Создать**.

### <a name="assign-an-initiative-definition"></a>Назначение определения инициативы

1. Перейдите на вкладку **Определения** в разделе **Разработка**.
2. Найдите созданное определение инициативы **Защита**.
3. Выберите определение инициативы, а затем щелкните **Назначить**.

   ![Назначение определения](media/create-manage-policy/assign-definition.png)

4. Заполните форму **Назначение**, введя:
   - Имя назначения: "Защита".
   - Описание: "Это назначение инициативы предназначено для принудительного применения группы определений политики в подписке **Azure Advisor Capacity Dev**".
   - Ценовая категория: "Стандартный".
   - Область применить назначения: **Azure Advisor Capacity Dev**.

5. Выберите **Назначить**.

## <a name="resolve-a-non-compliant-or-denied-resource"></a>Обход запрета на создание несоответствующих или отклоненных ресурсов

После назначения определения политики, требующего SQL Server версии 12.0, попытка создать SQL Server другой версии будет отклонена. В этом разделе мы переходим к разрешению отклоненной попытки создать сервер SQL Server другой версии.

1. Выберите **Назначения** в области слева.
2. Просмотрите все назначения политик и запустите назначение *Require SQL Server version 12.0* (Требовать наличия SQL Server версии 12.0).
3. Запросите исключение для групп ресурсов, в которых вы пытаетесь создать SQL Server. В этом случае исключите Microsoft.Sql/servers/databases: *baconandbeer/Cheetos* и *baconandbeer/Chorizo*.

   ![Исключение запроса](media/create-manage-policy/request-exclusion.png)

   Для решения проблемы с запрещенным ресурсом можно также обратиться к лицам, связанным с политикой, если у вас есть достаточное обоснование необходимости создания SQL Server, и выполнить прямое редактирование политики, если у вас есть доступ.

4. Щелкните **Сохранить**.

В этом разделе вы узнали, как обойти запрет создания SQL Server с версией 12.0, запросив исключение для ресурсов.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы намерены переходить к ним, не удаляйте ресурсы, которые вы создали при работе с этим руководством. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные назначения и определения.

1. Выберите **Определения** (или **Назначения**, если вы пытаетесь удалить назначение) в области слева.
2. Найдите новую инициативу либо определение политики (или назначение), которые вы только что создали.
3. Щелкните многоточие в конце определения или назначения, а затем выберите **Удалить определение** (или **Удалить назначение**).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы успешно выполнили следующие действия:

> [!div class="checklist"]
> * назначили политику для применения условий для ресурсов, которые будут созданы в будущем;
> * создали и назначили определение инициативы для отслеживания соответствия нескольких ресурсов;
> * обошли запрет на создание несоответствующих или отклоненных ресурсов;
> * внедрили новую политику в организации.

Дополнительные сведения о структурах определения политик см. в статье:

> [!div class="nextstepaction"]
> [Структура определения политики Azure](policy-definition.md)
