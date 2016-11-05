---
title: Шаблон диспетчера ресурсов для блокировок ресурсов | Microsoft Docs
description: Демонстрирует схему диспетчера ресурсов для развертывания блокировок ресурсов с помощью шаблона.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz

---
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

| Имя | Значение |
| --- | --- |
| type |Перечисление.<br />Обязательное значение.<br />**{пространство\_имен}/{тип}/providers/locks** для ресурсов или<br />**Microsoft.Authorization/locks** для групп ресурсов.<br /><br />Тип создаваемого ресурса. |
| версия\_API |Перечисление.<br />Обязательное значение.<br />**2015-01-01**<br /><br />Версия API, которая будет использована для создания ресурса. |
| name |Строка.<br />Обязательное значение.<br />**{ресурс}/Microsoft.Authorization/{имя\_блокировки}** для ресурсов или<br />**{имя\_блокировки}** для групп ресурсов.<br />До 64 знаков. Запрещены знаки <, >, %, &, ? и любые управляющие символы.<br /><br />Значение, определяющее блокируемый ресурс и имя блокировки. |
| Свойство dependsOn |Массив.<br />Необязательное значение.<br />Разделенный запятыми список имен или уникальных идентификаторов ресурсов.<br /><br />Коллекция ресурсов, от которых зависит эта блокировка. Если блокируемый ресурс развернут в том же шаблоне, включите его имя в этот элемент, чтобы гарантировать, что ресурс разворачивается первым. |
| properties |Объект.<br />Обязательное значение.<br />[Объект свойств.](#properties)<br /><br />Объект, определяющий тип блокировки, и примечания о блокировке. |

<a id="properties" />

### объект properties
| Имя | Значение |
| --- | --- |
| level |Перечисление.<br />Обязательное значение.<br />**CannotDelete**<br /><br />Тип блокировки для применения к области. Блокировка CanNotDelete разрешает изменение, но не допускает удаление. |
| HDInsight |Строка.<br />Необязательное значение.<br />До 512 знаков.<br /><br />Описание блокировки. |

## Использование ресурса блокировки
Добавьте этот ресурс в шаблон, чтобы не допустить выполнения указанных действий с ресурсом. Блокировка применяется ко всем пользователям и группам. Как правило, блокировка применяется в течение ограниченного времени, например во время выполнения процесса, если требуется защитить ресурс от неумышленного изменения или удаления другими сотрудниками организации.

Для создания или удаления блокировок управления необходимо иметь доступ к действиям **Microsoft.Authorization/*** или **Microsoft.Authorization/locks/***. Из встроенных ролей эти действия могут выполнять только роли **Владелец** и **Администратор доступа пользователей**. Сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](active-directory/role-based-access-control-configure.md).

Блокировка применяется к указанному ресурсу и любым дочерним ресурсам.

Можно снять блокировку с помощью команды PowerShell **Remove-AzureRmResourceLock** или [операции delete](https://msdn.microsoft.com/library/azure/mt204562.aspx) API REST.

## Примеры
В следующем примере к веб-приложению применяется блокировка "запрещено удаление".

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
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

В следующем примере блокировка "запрещено удаление" применяется к группе ресурсов.

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
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## Дальнейшие действия
* Сведения о структуре шаблона см. в разделе [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
* Дополнительные сведения о блокировках см. в разделе [Блокировка ресурсов с использованием диспетчера ресурсов Azure](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0406_2016-->