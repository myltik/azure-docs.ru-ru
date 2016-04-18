<properties
   pageTitle="Шаблон диспетчера ресурсов для назначения ролей | Microsoft Azure"
   description="Демонстрирует схему диспетчера ресурсов для развертывания назначения ролей с помощью шаблона."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Назначения ролей — схема шаблона

Назначает пользователю, группе или субъекту-службе роль в указанной области.

## Формат схемы

Чтобы создать назначение роли, добавьте следующую схему в раздел ресурсов шаблона.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
        "apiVersion": "2014-10-01-preview",
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

| Имя | Значение |
| ---- | ---- |
| type | Перечисление.<br />Обязательное значение.<br />**Microsoft.Authorization/roleAssignments**<br /><br />Тип создаваемого ресурса. |
| версия\_API | Перечисление.<br />Обязательное значение.<br />**2014-10-01-preview**<br /><br />Версия API, которая будет использована для создания ресурса. |  
| name | Строка.<br />Обязательное значение.<br />**Глобальный уникальный идентификатор**<br /><br />Идентификатор нового назначения роли. |
| Свойство dependsOn | Массив.<br />Необязательное значение.<br />Разделенный запятыми список имен или уникальных идентификаторов ресурсов.<br /><br />Коллекция ресурсов, от которых зависит это назначение роли. При назначении роли в области действия ресурса, который разворачивается в том же шаблоне, включите имя ресурса в этот элемент, чтобы гарантировать, что ресурс разворачивается первым. | 
| properties | Объект.<br />Обязательное значение.<br />[Объект свойств.](#properties)<br /><br />Объект, идентифицирующий определение роли, субъект и область. |  

<a id="properties" />
### объект properties

| Имя | Значение |
| ------- | ---- |
| roleDefinitionId | Строка.<br />Обязательное значение.<br /> **/subscriptions/{идентификатор-подпики}/providers/Microsoft.Authorization/roleDefinitions/{идентификатор-определения-роли}**<br /><br />Идентификатор существующего определения роли для назначения роли. |
| principalId | Строка.<br />Обязательное значение.<br />**Глобальный уникальный идентификатор**<br /><br />Идентификатор существующего субъекта. Он связывается с идентификатором в каталоге и может указывать на пользователя, субъекта-службу или группу безопасности. |
| scope | Строка.<br />Обязательное значение.<br />**/ subscriptions/{идентификатор-подписки}/resourceGroups/{имя-группы-ресурсов}** (для групп ресурсов) или<br />**/subscriptions/{идентификатор-подписки}/resourceGroups/{имя-группы-ресурсов}/providers/{пространство-имен-поставщика}/{тип-ресурса}/{имя-ресурса}** (для ресурсов).<br /><br />Область, к которой применяется это назначение роли. |


## Использование ресурсов назначения роли

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

## Шаблоны быстрого запуска

Следующие шаблоны демонстрируют использование ресурса назначения роли:

- [Назначение встроенной роли группе ресурсов](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Назначение встроенной роли существующей виртуальной машине](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Назначение встроенной роли нескольким существующим виртуальным машинам](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## Дальнейшие действия

- Сведения о структуре шаблона см. в разделе [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
- Дополнительные сведения об управлении доступом на основе ролей см. в статье [Контроль доступа на основе ролей в Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0406_2016-->