---
title: "Политики Azure Resource Manager | Документация Майкрософт"
description: "В этом разделе описывается, как предотвращать нарушения в различных областях, таких как подписки, ресурсы и группы ресурсов, с помощью политик диспетчера ресурсов Azure."
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2016
ms.author: gauravbh;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: bdc759341e1f9707ddf688512249c3297d85c29b


---
# <a name="use-policy-to-manage-resources-and-control-access"></a>Применение политик для управления ресурсами и контроля доступа
Теперь диспетчер ресурсов Azure позволяет управлять доступом с помощью пользовательских политик. С помощью политик можно запретить пользователям в организации нарушать соглашения, которые необходимы для управления ресурсами организации. 

Можно создать определения политик, которые описывают действия или ресурсы, выполнение которых или доступ к которым запрещен. Эти определения политик назначаются для нужной области, такой как подписка, группа ресурсов или отдельный ресурс. Политики наследуются всеми дочерними ресурсами. Это значит, что политики, применяемые к группе ресурсов, применяются также ко всем ресурсам в этой группе.

В этой статье мы продемонстрируем базовую структуру языка, который используется для создания политик. Затем мы опишем, как можно применить эти политики в различных ситуациях.

## <a name="how-is-it-different-from-rbac"></a>Чем это отличается от управления доступом на основе ролей?
Между политикой и контролем доступа на основе ролей существует ряд ключевых различий, но прежде всего необходимо понять, что политики и RBAC работают вместе. Чтобы использовать политики, нужно пройти аутентификацию с помощью RBAC. В отличие от RBAC, политика представляет собой систему разрешения по умолчанию и явного запрета. 

Основное внимание RBAC уделяет действиям, которые может выполнять **пользователь** в различных областях. Например, определенный пользователь добавляется в роль участника для группы ресурсов в требуемой области, после чего он может вносить изменения в эту группу ресурсов. 

Политика концентрируется на действиях **ресурсов** в различных областях. Например, с помощью политик можно управлять типами ресурсов, которые могут быть подготовлены, или ограничить расположения, в которых могут быть подготовлены ресурсы.

## <a name="common-scenarios"></a>Распространенные сценарии
Один из распространенных сценариев — запрос тегов подразделений для осуществления взимания средств за использование. Организация может разрешить выполнение операций только при указании соответствующего места возникновения затрат. В противном случае запрос будет отклонен. Эта политика позволит организации взимать плату за произведенные операции по месту возникновения соответствующих затрат.

Другой типичный сценарий — это контроль за местами создания ресурсов. Кроме того, организация может контролировать доступ к ресурсам, разрешая подготовку только определенного типа ресурсов.

Таким же образом организации могут управлять каталогом услуг или применять желаемые соглашения об именовании ресурсов.

Политики позволяют легко реализовать эти сценарии.

## <a name="policy-definition-structure"></a>Структура определения политики
Определение политики создается с использованием JSON. Оно состоит из одного или нескольких условий, или логических операторов, определяющих действия и результат, который будет достигнут при выполнении этих условий. Схема публикуется в [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

Как правило, политика содержит следующие элементы:

**Условие или логические операторы** — набор условий, которыми можно управлять с помощью набора логических операторов.

**Результат** — то, что происходит при выполнении условия (запрет или аудит). В случае аудита откроется журнал службы событий предупреждений. Например, администратор может создать политику, которая включает аудит, если какой-либо пользователь создал большую виртуальную машину. После этого администратор может просмотреть журналы.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## <a name="policy-evaluation"></a>Оценка политики
Политики вычисляются при создании ресурсов. При развертывании шаблона политики оцениваются во время создания каждого ресурса в шаблоне. 

> [!NOTE]
> Сейчас политика не вычисляет типы ресурсов, которые не поддерживают теги, вид и расположение, например тип ресурса Microsoft.Resources/deployments. Их поддержка будет добавлена в будущем. Чтобы избежать проблем с обратной совместимостью, необходимо явно указывать тип при создании политик. Например, политика тегов, которая не указывает типы, применяется для всех типов. В этом случае может произойти ошибка развертывания шаблона, если существует вложенный ресурс, который не поддерживает тег, и в вычисление политики был добавлен тип ресурса развертывания. 
> 
> 

## <a name="logical-operators"></a>Логические операторы
Поддерживаются следующие логические операторы и синтаксис:

| Имя оператора | Синтаксис |
|:--- |:--- |
| not |"not" : {&lt;условие или оператор&gt;} |
| и |"allOf" : [{&lt;условие или оператор&gt;},{&lt;условие или оператор&gt;}] |
| или |"anyOf" : [{&lt;условие или оператор&gt;},{&lt;условие или оператор&gt;}] |

Диспетчер ресурсов позволяет задать сложную логическую схему в политике через вложенные операторы. Например, вы можете запретить создание ресурсов в определенном месте для указанного типа ресурса. Пример вложенных операторов приведен в данном разделе.

## <a name="conditions"></a>Условия
Условие определяет, соответствует ли **поле** или **источник** определенным условиям. Ниже перечислены поддерживаемые условия и синтаксис:

| Имя условия | Синтаксис |
|:--- |:--- |
| Равно |"equals" : "&lt;значение&gt;" |
| Like |"like" : "&lt;значение&gt;" |
| Содержит |"contains" : "&lt;значение&gt;" |
| В |"in" : [ "&lt;значение1&gt;","&lt;значение2&gt;" ] |
| ContainsKey |"containsKey" : "&lt;имя_ключа&gt;" |
| Exists |"exists" : "&lt;логическое значение&gt;" |

### <a name="fields"></a>Поля
Условия формируются с помощью полей и источников. Поле представляет свойства в полезных данных запроса ресурса, используемые для описания состояния ресурса. Источник представляет характеристики самого запроса. 

Поддерживаются следующие поля и источники.

Поля: **name**, **kind**, **type**, **location**, **tags**, **tags.*** и **property alias**. 

### <a name="property-aliases"></a>Псевдонимы свойств
Псевдоним свойства — это имя, которое можно использовать в определении политики для доступа к определенным свойствам типа ресурса, например параметрам и SKU. Он действует во всех версиях API, в которых существует это свойство. Псевдонимы можно получить с помощью REST API (поддержка PowerShell будет добавлена в будущем).

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

В следующем примере показано определение псевдонима. Как видите, псевдоним определяет пути в различных версиях API, даже если имя свойства изменено. 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

Сейчас поддерживается следующие псевдонимы.

| Имя псевдонима | Описание |
| --- | --- |
| {resourceType}/sku.name |Поддерживаемые типы ресурса: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles. |
| {resourceType}/sku.family |Поддерживаемый тип ресурса — Microsoft.Cache/Redis. |
| {resourceType}/sku.capacity |Поддерживаемый тип ресурса — Microsoft.Cache/Redis. |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |

В настоящее время политика работает только для запросов PUT. 

## <a name="effect"></a>Результат
Политика поддерживает три типа результатов — **deny**, **audit** и **append**. 

* "deny" — создание события в журнале аудита и сбой запроса.
* "audit" — создание события в журнале аудита и выполнение запроса.
* "append" — добавление определенного набора полей в запрос. 

Для типа **append**необходимо указать следующие сведения:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

Значением может быть строка или объект формата JSON. 

## <a name="policy-definition-examples"></a>Примеры определений политик
Теперь давайте рассмотрим, как определить политику для реализации предыдущих сценариев.

### <a name="chargeback-require-departmental-tags"></a>Взимание средств за использование: требовать теги отделов
Следующая политика отклоняет запросы без тега costCenter.

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

Следующая политика добавляет тег costCenter с предустановленным значением, если теги отсутствуют. 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }

Следующая политика добавляет тег costCenter с предустановленным значением при отсутствии указанного тега и присутствии других тегов. 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]

      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>Соблюдение географических границ: контроль расположения ресурсов
В следующем примере показана политика, отклоняющая все запросы с размещением, отличным от Северной или Западной Европы.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>Контроль служб: выбор каталога служб
В следующем примере показана политика, допускающая выполнение действий только со службами типа Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\*. Действия с остальными службами отклоняются.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>Использование утвержденных SKU
В следующем примере показано, как использовать псевдоним свойства для ограничения номеров SKU. В примере для учетных записей хранения утверждены только Standard_LRS и Standard_GRS.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }


### <a name="naming-convention"></a>Соглашение об именовании
В следующем примере показано, как использовать знак подстановки, который поддерживает условие like. Это условие означает, что если имя не соответствует заданному шаблону (namePrefix\*nameSuffix), запрос будет отклонен.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="tag-requirement-just-for-storage-resources"></a>Требование помечать тегами только ресурсы хранилища
В следующем примере показано, как вложить логические операторы, согласно которым приложение будет помечать тегами только ресурсы хранилища.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="create-and-assign-a-policy"></a>Создание и назначение политики
Чтобы применить политику, требуется создать ее определение, а затем применить ее к какой-либо области. 

### <a name="rest-api"></a>Интерфейс REST API
Для создания политики вы можете использовать [API REST для определений политик](https://docs.microsoft.com/rest/api/resources/policydefinitions). API REST позволяет создавать и удалять определения политик и получать сведения о существующих определениях.

Чтобы создать политику, выполните следующую команду:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

В качестве версии API (api-version) используйте значение *2016-04-01*. Добавьте текст запроса. Ниже приведен соответствующий пример.

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }

Вы можете применить определение политики к требуемой области с помощью [API REST для назначений политик](https://docs.microsoft.com/rest/api/resources/policyassignments). API REST позволяет создавать и удалять назначения политик и получать сведения о существующих назначениях.

Чтобы создать назначение политики, выполните следующую команду:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} — это имя назначения политики. В качестве версии API (api-version) используйте значение *2016-04-01*. 

Тело запроса будет выглядеть, как в следующем примере:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

### <a name="powershell"></a>PowerShell
Определение политики можно создать с помощью командлета New-AzureRmPolicyDefinition. В следующем примере создается политика, разрешающая использовать только ресурсы в Северной и Западной Европе.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'            

Результат выполнения сохраняется в объекте $policy и может использоваться позднее во время назначения политики. Вместо встроенного указания политики для параметра политики также можно указать путь к JSON-файлу, содержащему политику.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

Политику можно применить к требуемой области с помощью командлета PowerShell New-AzureRmPolicyAssignment.

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Здесь $policy — это объект политики, который был возвращен в результате выполнения командлета New-AzureRmPolicyDefinition. Область — это указанное имя группы ресурсов.

Чтобы удалить назначение политики, воспользуйтесь приведенной ниже командой.

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Определения политик можно получать, изменять или удалять с помощью командлетов Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition и Remove-AzureRmPolicyDefinition соответственно.

Аналогично вы можете получать, изменять или удалять назначения политик с помощью командлетов Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment и Remove-AzureRmPolicyAssignment соответственно.

### <a name="azure-cli"></a>Инфраструктура CLI Azure
Определение политики можно создать с помощью интерфейса командной строки Azure, выполнив в нем команду определения политики. В следующем примере создается политика, разрешающая использовать только ресурсы в Северной и Западной Европе.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    


Вместо указания политики в командной строке можно указать путь к JSON-файлу, содержащему политику.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"

Политику можно применить к требуемой области с помощью команды назначения политики.

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Область — это указанное имя группы ресурсов. Если значение параметра policy-definition-id неизвестно, то его можно получить с помощью интерфейса командной строки Azure. 

    azure policy definition show <policy-name>

Чтобы удалить назначение политики, воспользуйтесь приведенной ниже командой.

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

С помощью команд policy definition show, policy definition set и policy definition delete можно соответственно получать, изменять или удалять определения политик.

Аналогичным образом с помощью команд policy assignment show, policy assignment set и policy assignment delete можно получать, изменять или удалять назначения политик.

## <a name="policy-audit-events"></a>События аудита политики
После применения политики вы начнете видеть связанные с ней события. Для получения этих данных вы можете перейти на портал, воспользоваться PowerShell или интерфейсом командной строки Azure. 

### <a name="powershell"></a>PowerShell
Чтобы просмотреть все события, связанные с результатом запрета, можно использовать следующую команду PowerShell:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Чтобы просмотреть все события, связанные с результатом аудита, можно использовать следующую команду:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="azure-cli"></a>Инфраструктура CLI Azure
Чтобы просмотреть все события в группе ресурсов, связанные с результатом запрета, можно использовать следующую команду интерфейса командной строки:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

Чтобы просмотреть все события, связанные с результатом аудита, можно использовать следующую команду интерфейса командной строки:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

## <a name="view-a-policy"></a>Просмотр политики
Для просмотра политики можно использовать PowerShell, интерфейс командной строки Azure (Azure CLI) или REST API. Просмотреть политику может потребоваться после сбоя развертывания, когда требуется просмотреть правило, помешавшее выполнить развертывание. Сообщение об ошибке содержит идентификатор определения политики.

### <a name="powershell"></a>PowerShell
Чтобы получить политику, используйте следующий командлет.

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json

Он возвращает код JSON определения политики.

### <a name="azure-cli"></a>Инфраструктура CLI Azure
Чтобы получить политику, используйте следующую команду.

    azure policy definition show {definition-name} --json

### <a name="rest-api"></a>Интерфейс REST API
Чтобы получить политику, используйте операцию [получения определения политики](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

## <a name="next-steps"></a>Дальнейшие действия
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).




<!--HONumber=Nov16_HO3-->


