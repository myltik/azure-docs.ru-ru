---
title: "Шаблон Resource Manager для блокировок ресурсов | Документация Майкрософт"
description: "Демонстрирует схему диспетчера ресурсов для развертывания блокировок ресурсов с помощью шаблона."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>Блокировки ресурсов — схема шаблона
Создает блокировку для ресурса и его дочерних ресурсов.

## <a name="schema-format"></a>Формат схемы
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



## <a name="values"></a>Значения
В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Обязательно | Description (Описание) |
| --- | --- | --- |
| type |Да |Тип создаваемого ресурса.<br /><br />Для ресурсов:<br />**{пространство_имен}/{тип}/providers/locks**<br /><br/>Для групп ресурсов:<br />**Microsoft.Authorization/locks** |
| версия_API |Да |Версия API, которая будет использована для создания ресурса.<br /><br />Используйте следующую команду:<br />**2015-01-01**<br /><br /> |
| name |Да |Значение, указывающее блокируемый ресурс и имя блокировки. Может содержать до 64 знаков. Не может содержать знаки <, >, %, &, ? или управляющие символы.<br /><br />Для ресурсов:<br />**{ресурс}/Microsoft.Authorization/{имя_блокировки}**<br /><br />Для групп ресурсов:<br />**{имя_блокировки}** |
| Свойство dependsOn |Нет |Разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов.<br /><br />Коллекция ресурсов, от которых зависит эта блокировка. Если блокируемый ресурс развернут в том же шаблоне, включите его имя в этот элемент, чтобы гарантировать, что ресурс разворачивается первым. |
| properties |Да |Объект, определяющий тип блокировки, и примечания о блокировке.<br /><br />Ознакомьтесь с разделом [Объект properties](#properties-object). |

### <a name="properties-object"></a>объект properties
| Имя | Обязательно | Описание |
| --- | --- | --- |
| level |Да |Тип блокировки для применения к области.<br /><br />Блокировка **CannotDelete**: пользователи могут изменить ресурс, но не могут его удалить.<br />Блокировка **ReadOnly**: пользователи смогут читать данные из ресурса, но не смогут удалить его или производить с ним какие-либо действия. |
| HDInsight |Нет |Описание блокировки. Может содержать до 512 знаков. |

## <a name="how-to-use-the-lock-resource"></a>Использование ресурса блокировки
Добавьте этот ресурс в шаблон, чтобы не допустить выполнения указанных действий с ресурсом. Блокировка применяется ко всем пользователям и группам.

Для создания или удаления блокировок управления необходимо иметь доступ к действиям **Microsoft.Authorization/*** или **Microsoft.Authorization/locks/***. Из встроенных ролей эти действия предоставляются только **владельцу** и **администратору доступа пользователей**. Сведения о контроле доступа на основе ролей см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../active-directory/role-based-access-control-configure.md).

Блокировка применяется к указанному ресурсу и любым дочерним ресурсам.

Можно снять блокировку с помощью команды PowerShell **Remove-AzureRmResourceLock** или [операции delete](https://msdn.microsoft.com/library/azure/mt204562.aspx) API REST.

## <a name="examples"></a>Примеры
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

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о структуре шаблона см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Дополнительные сведения о блокировках см. в разделе [Блокировка ресурсов с использованием диспетчера ресурсов Azure](resource-group-lock-resources.md).




<!--HONumber=Nov16_HO3-->


