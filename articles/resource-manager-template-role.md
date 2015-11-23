<properties
   pageTitle="Шаблон диспетчера ресурсов для назначения ролей | Microsoft Azure"
   description="Показывает схему диспетчера ресурсов для создания назначения ролей во время развертывания."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="tomfitz"/>

# Назначения ролей — схема шаблона

Назначает пользователю, группе или субъекту-службе роль в указанной области.

## Формат схемы

Чтобы создать назначение роли, добавьте следующую схему в раздел ресурсов шаблона.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
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

## Значения

В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Да | **Microsoft.Authorization/roleAssignments** | Тип создаваемого ресурса. |
| версия\_API | enum | Да | **2015-07-01** | Версия API, которая будет использована для создания ресурса. |  
| name | строка | Да | Глобальный уникальный идентификатор | Идентификатор для нового назначения роли. |
| Свойство dependsOn | array | Нет | Разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. | Коллекция ресурсов, от которых зависит это назначение роли. При назначении роли в области действия ресурса, который разворачивается в том же шаблоне, включите имя ресурса в этот элемент, чтобы гарантировать, что ресурс разворачивается первым. | 
| properties | object | Да | (показано ниже) | Объект, задающий определение роли, субъекта и область. |  

### объект properties

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ------- | ---- | ---------------- | -------- | ----------- |
| roleDefinitionId | string | Да | **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** | Идентификатор существующего определения роли для использования в назначении ролей. |
| principalId | string | Да | Глобальный уникальный идентификатор | Идентификатор существующего субъекта. Он связывается с идентификатором в каталоге и может указывать на пользователя, субъекта-службу или группу безопасности. |
| scope | string | Да | Для группы ресурсов:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**<br /><br />Для ресурса:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** | Область, в которой применяется это назначение роли. |


## Использование ресурса блокировки

Вы добавляете назначение ролей к шаблону, если нужно добавить к роли пользователя, группу или субъекта-службу во время развертывания. Назначения ролей наследуются с более высоких уровней области, поэтому если субъект уже добавлен к роли на уровне подписки, то не нужно повторно назначать его для группы ресурсов или ресурса.

Новый идентификатор для **name** можно создать с помощью следующей команды:

    PS C:\> $name = [System.Guid]::NewGuid().toString()

Получить глобальный уникальный идентификатор для определения роли можно с помощью следующей команды:

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Получить идентификатор субъекта можно с помощью одной из следующих команд.

Для группы с именем **Auditors**:

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Для пользователя с именем **exampleperson**:

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Для субъекта-службы с именем **exampleapp**:

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## Примеры

В следующем примере группа назначается роли для группы ресурсов.

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


## Дальнейшие действия

- Сведения о структуре шаблона см. в разделе [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
- Дополнительные сведения об управлении доступом на основе ролей см. в статье [Контроль доступа на основе ролей в Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=Nov15_HO3-->