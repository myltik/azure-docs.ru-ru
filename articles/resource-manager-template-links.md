<properties
   pageTitle="Шаблон диспетчера ресурсов для связывания ресурсов | Microsoft Azure"
   description="Демонстрирует схему диспетчера ресурсов для развертывания связей между связанными ресурсами с помощью шаблона."
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

# Связи ресурсов — схема шаблона

Создание связи между двумя ресурсами Связь применяется к ресурсу, известному как исходный ресурс. Второй ресурс в связи называется целевым ресурсом.

## Формат схемы

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



## Значения

В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Значение |
| ---- | ---- |
| type | Перечисление.<br />Обязательное значение.<br />**{пространство\_имен}/{тип}/providers/links**<br /><br />Тип создаваемого ресурса. Значения {namespace} и {type} ссылаются на пространство имен поставщика и тип ресурса исходного ресурса. |
| версия\_API | Перечисление.<br />Обязательное значение.<br />**2015-01-01**<br /><br />Версия API, которая будет использована для создания ресурса. |  
| name | Строка.<br />Обязательное значение.<br />**{ресурс}/Microsoft.Resources/{имя\_связи}**<br />До 64 знаков. Запрещается использовать <, >, %, &, ? и любые управляющие символы. <br /><br />Значение, указывающее одновременно имя ресурса и имя связи. | 
| dependsOn | Массив<br />Необязательное значение.<br />Разделенный запятыми список имен или уникальных идентификаторов ресурсов.<br /><br />Коллекция ресурсов, от которых зависит эта связь. Если связываемые ресурсы развернуты в одном и том же шаблоне, включите имена этих ресурсов в данный элемент, чтобы гарантировать, что они будут развернуты первыми. | 
| properties | Объект.<br />Обязательное значение.<br />[Объект свойств.](#properties)<br /><br />Объект, идентифицирующий связываемый ресурс, и примечания о связи. | 

<a id="properties" />
### объект properties

| Имя | Значение |
| ------- | ---- |
| targetId | Строка.<br />Обязательное значение.<br />**{идентификатор ресурса}**<br /><br />Идентификатор целевого ресурса для связывания. || notes | Строка.<br />Необязательное значение.<br />До 512 знаков.<br /><br />Описание блокировки. |


## Использование ресурса связи

Связь между двумя ресурсами устанавливается, когда ресурсы имеют зависимость, продолжающуюся после развертывания. Например, приложение может подключаться к базе данных в другой группе ресурсов. Эту зависимость можно определить, создав связь из приложения к базе данных. Связи позволяют документировать отношение между двумя ресурсами. Позже вы или кто-то другой в вашей организации может запросить ресурс на получение связей, чтобы узнать, как ресурс работает с другими ресурсами.

Все связанные ресурсы должны принадлежать к одной подписке. Каждый ресурс может быть связан с 50 другими ресурсами. При удалении или перемещении каких-либо связанных ресурсов владельцу связи нужно удалить оставшуюся связь.

Сведения о работе со связями с помощью REST см. в статье [Связанные ресурсы](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Чтобы просмотреть все связи в подписке, используйте следующую команду Azure PowerShell. Можно указать другие параметры, чтобы ограничить результаты.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

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

## Шаблоны быстрого запуска

Следующие шаблоны быстрого запуска служат для развертывания ресурсов с помощью связей.

- [Оповещение для очереди с помощью приложения логики](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Оповещение для Slack с помощью приложения логики](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Подготовка приложения API с помощью существующего шлюза](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Подготовка приложения API с помощью нового шлюза](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Создание приложения логики и приложения API с помощью шаблона](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [Приложение логики, которое отправляет текстовое сообщение при возникновении предупреждения](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## Дальнейшие действия

- Сведения о структуре шаблона см. в разделе [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0406_2016-->