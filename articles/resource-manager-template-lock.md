<properties
   pageTitle="Шаблон диспетчера ресурсов для блокировок ресурсов | Microsoft Azure"
   description="Демонстрирует схему диспетчера ресурсов для развертывания блокировок ресурсов с помощью шаблона."
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
   ms.date="01/04/2016"
   ms.author="tomfitz"/>

# Блокировки ресурсов — схема шаблона

Создает новую блокировку на ресурс и его дочерние ресурсы.

## Формат схемы

Чтобы создать блокировку, добавьте следующую схему в раздел ресурсов шаблона.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## Значения

В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Да | Для ресурсов: <br />**{namespace}/{type}/providers/locks**<br /><br />Для групп ресурсов:<br />**Microsoft.Authorization/locks** | Тип создаваемого ресурса. |
| версия\_API | enum | Да | **2015-01-01** | Версия API, которая будет использована для создания ресурса. |  
| name | строка | Да | Для ресурсов: <br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />Для групп ресурсов:<br />**{lockname}****<br /><br />до 64 символов<br />Запрещается использовать <, > %, &, ? и любые контрольные символы. | Значение, указывающее одновременно и на блокируемый ресурс, и на имя блокировки. | | dependsOn | массив | Нет | Разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. | Коллекция ресурсов, от которых зависит эта блокировка. Если блокируемый ресурс развернут в том же шаблоне, включите имя этого ресурса в данный элемент, чтобы гарантировать, что ресурс будет развернут первым. | | свойства | объект | Да | (показано ниже) | Объект, идентифицирующий тип блокировки, и примечания о блокировке. | 

### объект properties

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ------- | ---- | ---------------- | -------- | ----------- |
| level | enum | Да | **CannotDelete** <br /> **ReadOnly** | Тип блокировки для применения к области. CanNotDelete позволяет вносить изменения, но не позволяет удалять, ReadOnly не позволяет ни вносить изменения, ни удалять. |
| HDInsight | строка | Нет | 512 символов | Описание блокировки. |


## Использование ресурса блокировки

Добавьте этот ресурс в шаблон, чтобы не допустить выполнения указанных действий с ресурсом. Блокировка применяется ко всем пользователям и группам. Как правило, блокировка применяется в течение ограниченного времени, например во время выполнения процесса, если требуется защитить ресурс от неумышленного изменения или удаления другими сотрудниками организации.

Для создания или удаления блокировок управления необходимо иметь доступ к действиям **Microsoft.Authorization/*** или **Microsoft.Authorization/locks/***. Из встроенных ролей эти действия могут выполнять только роли **Владелец** и **Администратор доступа пользователей**. Сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

Блокировка применяется к указанному ресурсу и любым дочерним ресурсам. Если к ресурсу применяется более одной блокировки, приоритет имеет наиболее строгая блокировка. Например, если применить ReadOnly на уровне родителя (например, для группы ресурсов) и CanNotDelete к ресурсу в пределах этой группы, то приоритет будет иметь более строгая блокировка (ReadOnly) родительского элемента.

Можно снять блокировку с помощью команды PowerShell **Remove-AzureRmResourceLock** или [операции delete](https://msdn.microsoft.com/library/azure/mt204562.aspx) API REST.

## Примеры

В следующем примере к веб-приложению применяется блокировка «только чтение».

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
      			"type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "ReadOnly",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

В следующем примере блокировка «только чтение» применяется к группе ресурсов.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "ReadOnly",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## Дальнейшие действия

- Сведения о структуре шаблона см. в разделе [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
- Дополнительные сведения о блокировках см. в разделе [Блокировка ресурсов с использованием диспетчера ресурсов Azure](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0107_2016-->