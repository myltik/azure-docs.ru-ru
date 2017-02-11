---
title: "Шаблон Resource Manager для связывания ресурсов | Документация Майкрософт"
description: "Демонстрирует схему диспетчера ресурсов для развертывания связей между связанными ресурсами с помощью шаблона."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 48a13b1a-3208-4f91-ba85-bda2a0e22605
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: dcac45042b2511ae2b82ecf8278e9873c6f8c9b9


---
# <a name="resource-links-template-schema"></a>Связи ресурсов — схема шаблона
Создание связи между двумя ресурсами Связь применяется к ресурсу, известному как исходный ресурс. Второй ресурс в связи называется целевым ресурсом.

## <a name="schema-format"></a>Формат схемы
Чтобы создать связь, добавьте следующую схему в раздел ресурсов шаблона.

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Значения
В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Значение |
| --- | --- |
| type |Перечисление.<br />Обязательно<br />**{пространство_имен}/{тип}/providers/links**<br /><br />Тип создаваемого ресурса. Значения {namespace} и {type} ссылаются на пространство имен поставщика и тип ресурса исходного ресурса. |
| версия_API |Перечисление.<br />Обязательно<br />**2015-01-01**<br /><br />Версия API, которая будет использована для создания ресурса. |
| Имя |Строка<br />Обязательно<br />**{ресурс}/Microsoft.Resources/{имя_связи}**<br /> До 64 знаков. Не может содержать знаки <, >, %, &, ? или управляющие символы.<br /><br />Значение, определяющее имя исходного ресурса и имя для связи. |
| Свойство dependsOn |Массив,<br />Необязательно<br />Разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов.<br /><br />Коллекция ресурсов, от которых зависит эта связь. Если связываемые ресурсы развертываются в том же шаблоне, включают имена этих ресурсов в этом элементе, чтобы убедиться, что они развертываются сначала. |
| properties |Объект<br />Обязательно<br />[объект properties](#properties)<br /><br />Объект, который определяет ресурс, с которым выполняется связь, и заметки о связи. |

<a id="properties" />

### <a name="properties-object"></a>объект properties
| Имя | Значение |
| --- | --- |
| targetId |Строка<br />Обязательно<br />**{идентификатор_ресурса}**<br /><br />Идентификатор целевого ресурса для связывания. |
| HDInsight |Строка<br />Необязательно<br />До 512 знаков.<br /><br />Описание блокировки. |

## <a name="how-to-use-the-link-resource"></a>Использование ресурса связи
Связь между двумя ресурсами устанавливается, когда ресурсы имеют зависимость, продолжающуюся после развертывания. Например, приложение может подключаться к базе данных в другой группе ресурсов. Эту зависимость можно определить, создав связь из приложения к базе данных. Связи позволяют документировать отношение между двумя ресурсами. Позже вы или кто-то другой в вашей организации может запросить ресурс на получение связей, чтобы узнать, как ресурс работает с другими ресурсами.

Все связанные ресурсы должны принадлежать к одной подписке. Каждый ресурс может быть связан с 50 другими ресурсами. При удалении или перемещении каких-либо связанных ресурсов владельцу связи нужно удалить оставшуюся связь.

Сведения о работе со связями с помощью REST см. в статье [Связанные ресурсы](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Чтобы просмотреть все связи в подписке, используйте следующую команду Azure PowerShell. Можно указать другие параметры, чтобы ограничить результаты.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Примеры
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
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Шаблоны быстрого запуска
Следующие шаблоны быстрого запуска служат для развертывания ресурсов с помощью связей.

* [Оповещение для очереди с помощью приложения логики](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
* [Оповещение для Slack с помощью приложения логики](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
* [Подготовка приложения API с помощью существующего шлюза](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
* [Подготовка приложения API с помощью нового шлюза](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
* [Создание приложения логики и приложения API с помощью шаблона](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
* [Приложение логики, которое отправляет текстовое сообщение при возникновении предупреждения](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о структуре шаблона см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).




<!--HONumber=Nov16_HO3-->


