---
title: "Шаблон Resource Manager для назначений ролей | Документация Майкрософт"
description: "Демонстрирует схему диспетчера ресурсов для развертывания назначения ролей с помощью шаблона."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: d9541169-00d1-4497-b57f-68896d51849b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 69767db0399d02b82495da475cb8c5bf25710c87


---
# <a name="role-assignments-template-schema"></a>Назначения ролей — схема шаблона
Назначает пользователю, группе или субъекту-службе роль в указанной области.

## <a name="resource-format"></a>Формат ресурсов
Чтобы создать назначение роли, добавьте следующую схему в раздел ресурсов шаблона.

    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Значения
В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Обязательно | Description (Описание) |
| --- | --- | --- |
| type |Да |Тип создаваемого ресурса.<br /><br /> Для группы ресурсов:<br />**Microsoft.Authorization/roleAssignments**<br /><br />Для ресурса:<br />**{provider-namespace}/{resource-type}/providers/roleAssignments** |
| версия_API |Да |Версия API, которая будет использована для создания ресурса.<br /><br /> Используйте **2015-07-01**. |
| name |Да |Глобальный уникальный идентификатор для нового назначения роли. |
| Свойство dependsOn |Нет |Разделенный запятыми массив имен ресурсов или уникальных идентификаторов ресурсов.<br /><br />Коллекция ресурсов, от которых зависит это назначение роли. При назначении роли в области действия ресурса, который разворачивается в том же шаблоне, включите имя ресурса в этот элемент, чтобы гарантировать, что ресурс разворачивается первым. |
| properties |Да |Объект свойств, задающий определение роли, субъекта и область. |

### <a name="properties-object"></a>объект properties
| Имя | Обязательно | Описание |
| --- | --- | --- |
| roleDefinitionId |Да |Идентификатор существующего определения роли для использования в назначении ролей.<br /><br /> Используйте следующий формат:<br /> **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId |Да |Глобальный уникальный идентификатор для существующего субъекта. Это значение связывается с идентификатором в каталоге и может указывать на пользователя, субъекта-службу или группу безопасности. |
| scope |Нет |Область, в которой применяется это назначение роли.<br /><br />Для групп ресурсов используйте:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**  <br /><br />Для ресурсов используйте:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |

## <a name="how-to-use-the-role-assignment-resource"></a>Использование ресурсов назначения роли
Вы добавляете назначение ролей к шаблону, если нужно добавить к роли пользователя, группу или субъекта-службу во время развертывания. Назначения ролей наследуются от более высоких уровней области, поэтому если субъект уже добавлен к роли на уровне подписки, не нужно повторно назначать его для группы ресурсов или ресурса.

Существует множество значений идентификаторов, которые необходимо предоставлять при работе с назначениями ролей. Значения можно извлечь посредством PowerShell или интерфейса командной строки Azure.

### <a name="powershell"></a>PowerShell
Для имени назначения роли требуется глобальный уникальный идентификатор. Новый идентификатор для **name** можно создать с помощью следующей команды:

    $name = [System.Guid]::NewGuid().toString()

Идентификатор для определения роли можно получить с помощью следующей команды:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Получить идентификатор субъекта можно с помощью одной из следующих команд.

Для группы с именем **Auditors**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Для пользователя с именем **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Для субъекта-службы с именем **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Инфраструктура CLI Azure
Идентификатор для определения роли можно получить с помощью следующей команды:

    azure role show Reader --json | jq .[].Id -r

Получить идентификатор субъекта можно с помощью одной из следующих команд.

Для группы с именем **Auditors**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Для пользователя с именем **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Для субъекта-службы с именем **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Примеры
Следующий шаблон получает идентификатор для роли и идентификатор для пользователя, группы или субъекта-службы. Он назначает роль на уровне группы ресурсов.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



Следующий шаблон создает учетную запись хранения и присваивает ей роль читателя. Идентификаторы двух групп и роль читателя включены в шаблон для упрощения развертывания. Эти значения можно получить во время развертывания с помощью сценария и передать в качестве параметров.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Шаблоны быстрого запуска
Следующие шаблоны демонстрируют использование ресурса назначения роли:

* [Назначение встроенной роли группе ресурсов](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
* [Назначение встроенной роли существующей виртуальной машине](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
* [Назначение встроенной роли нескольким существующим виртуальным машинам](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о структуре шаблона см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Дополнительные сведения об управлении доступом на основе ролей см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../active-directory/role-based-access-control-configure.md).




<!--HONumber=Nov16_HO3-->


