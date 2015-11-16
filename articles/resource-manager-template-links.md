<properties
   pageTitle="Шаблон диспетчера ресурсов для связывания ресурсов | Microsoft Azure"
   description="Показывает схему шаблона диспетчера ресурсов для создания связей между связанными ресурсами."
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
   ms.date="10/31/2015"
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

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Да | **{namespace}/{type}/providers/links** | Тип создаваемого ресурса. Значения {namespace} и {type} ссылаются на пространство имен поставщика и тип ресурса исходного ресурса. |
| версия\_API | enum | Да | **2015-01-01** | Версия API, которая будет использована для создания ресурса. |  
| name | строка | Да | **{resouce}/Microsoft.Resources/{linkname}**<br /><br />до 64 символов<br />Не может содержать <, > %, &, ? или любые управляющие символы. | Значение, определяющее имя исходного ресурса и имя для связи. |
| Свойство dependsOn | array | Нет | Разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. | Коллекция ресурсов, от которых зависит эта связь. Если связываемые ресурсы развертываются в том же шаблоне, включают имена этих ресурсов в этом элементе, чтобы убедиться, что они развертываются сначала. | 
| properties | object | Да | (показано ниже) | Объект, который определяет ресурс, с которым выполняется связь, и заметки о связи. |  

### объект properties

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ------- | ---- | ---------------- | -------- | ----------- |
| tagetId | строка | Да | | Идентификатор целевого ресурса, с которым нужно установить связь. |
| HDInsight | строка | Нет | 512 символов | Описание блокировки. |


## Использование ресурса связи

Связь между двумя ресурсами устанавливается, когда ресурсы имеют зависимость, продолжающуюся после развертывания. Например, приложение может подключаться к базе данных в другой группе ресурсов. Эту зависимость можно определить, создав связь из приложения к базе данных. Связи позволяют документировать отношение между двумя ресурсами. Позже вы или кто-то другой в вашей организации может запросить ресурс на получение связей, чтобы узнать, как ресурс работает с другими ресурсами.

Все связанные ресурсы должны принадлежать к одной подписке. Каждый ресурс может быть связан с 50 другими ресурсами. При удалении или перемещении каких-либо связанных ресурсов владельцу связи нужно удалить оставшуюся связь.

Сведения о работе со связями с помощью REST см. в статье [Связанные ресурсы](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Чтобы просмотреть все связи в подписке, используйте следующую команду Azure PowerShell. Можно указать другие параметры, чтобы ограничить результаты.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -OutputObjectFormat New

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


## Дальнейшие действия

- Сведения о структуре шаблона см. в разделе [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).

<!---HONumber=Nov15_HO2-->