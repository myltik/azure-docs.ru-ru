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

| Имя | Значение | Описание |
| ---- | ---- | ---- |
| type | Перечисление,<br />обязательное.<br />**Microsoft.Authorization/roleAssignments** | Тип создаваемого ресурса. |
| версия\_API | Перечисление,<br />обязательное.<br />**2014-10-01-preview** | Версия API, которая будет использована для создания ресурса. |  
| name | Строка,<br />обязательное.<br />**Глобальный уникальный идентификатор.** | Идентификатор для нового назначения роли. |
| Свойство dependsOn | Массив,<br />необязательный.<br />Разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. | Коллекция ресурсов, от которых зависит это назначение роли. При назначении роли в области действия ресурса, который разворачивается в том же шаблоне, включите имя ресурса в этот элемент, чтобы гарантировать, что ресурс разворачивается первым. | 
| properties | Объект,<br />обязательный.<br />[Объект свойств](#properties). | Объект, задающий определение роли, субъекта и область. |  

<a id="properties" />

### Объект свойств

| Имя | Значение1 | Описание |
| ---- | ---- | ---- |
| roleDefinitionId | Строка, <br />обязательный. <br /><br /> **/subscriptions/{ИД\_подписки}/providers/Microsoft.Authorization/roleDefinitions/{ИД\_определения\_роли}** | Идентификатор существующего определения роли для использования в назначении ролей. |
| principalId | Строка,<br />обязательный. <br /><br /> **Глобальный уникальный идентификатор.** | Идентификатор существующего субъекта. Он связывается с идентификатором в каталоге и может указывать на пользователя, субъекта-службу или группу безопасности. |
| scope | Строка,<br />обязательный. <br /><br /> **/subscriptions/{ИД\_подписки}/resourceGroups/{группа\_ресурсов}** (для групп ресурсов)<br /> или <br />**/subscriptions/{ИД\_подписки}/resourceGroups/{группа\_ресурсов}/providers/{пространство\_имен\_поставщика}/{тип\_ресурса}/{имя\_ресурса}** (для ресурсов). | Область, в которой применяется это назначение роли. |


## Использование ресурсов назначения роли

Вы добавляете назначение ролей к шаблону, если нужно добавить к роли пользователя, группу или субъекта-службу во время развертывания. Назначения ролей наследуются от более высоких уровней области, поэтому если субъект уже добавлен к роли на уровне подписки, то не нужно повторно назначать его для группы ресурсов или ресурса.

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

<!---HONumber=AcomDC_0928_2016-->