<properties 
	pageTitle="Привязка ресурсов в диспетчере ресурсов Azure" 
	description="Создание связи между ресурсами в разных группах ресурсов в диспетчере ресурсов Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# Привязка ресурсов в диспетчере ресурсов Azure

После развертывания вам может потребоваться отправить запрос на установку связей между ресурсами. Зависимости выступают источником информации для развертывания, но во время развертывания этот жизненный цикл завершается. После завершения развертывания идентифицируемой связи между зависимыми ресурсами не существует.

Вместо этого диспетчер ресурсов Azure предоставляет новую функцию для установления и отправки запроса на установление связей между ресурсами, которая называется привязкой ресурсов. Вы можете определить, какие ресурсы будут привязаны к ресурсу или какие ресурсы будут привязаны из него.

Областью для ссылки на ресурс может быть подписка, группа ресурсов или определенный ресурс. Это означает, что ссылки на ресурсы могут содержать информацию о связях между группами ресурсов. При разделении развертывания на несколько шаблонов и групп ресурсов удобно использовать механизм определения ссылок на эти ресурсы. Например, база данных с определенным жизненным циклом может находиться в одной группе ресурсов, а приложение с иным жизненным циклом — в другой. Приложение подключается к базе данных, поэтому между ресурсами, находящимися в разных группах ресурсов, существует связь.

Все связанные ресурсы должны принадлежать к одной подписке. Каждый ресурс может быть связан с 50 другими ресурсами. При удалении или перемещении каких-либо связанных ресурсов владельцу связи нужно удалить оставшуюся связь.

## Привязка с использованием шаблонов

В приведенном ниже примере показан шаблон, создающий ресурс типа Microsoft.AppService/apiapps с набором однонаправленных связей с веб-сайтом, концентратором уведомлений и базами данных SQL.

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

## Создание связей с помощью REST API

Чтобы определить связь между развернутыми ресурсами, выполните следующую команду:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Замените {subscription-id} идентификатором вашей подписки. Замените {resource-group}, {provider-namespace, {resource-type}, и {resource-name} значениями, определяющими первый ресурс в этой связи. Замените {link-name} именем связи, которую нужно создать. Используйте API версии 2015-01-01.

В запросе включите объект, который определяет второй ресурс в связи:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

В элементе properties содержится идентификатор второго ресурса.

Дополнительные примеры, в том числе способы получения информации о связях, см. в разделе [Связанные ресурсы](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Дальнейшие действия

- Вы также можете организовать ресурсы с помощью тегов. Дополнительную информацию о маркировке ресурсов тегами см. в статье [Использование тегов для организации ресурсов](resource-group-using-tags.md).
- Описание способов создания шаблонов и определения ресурсов для развертывания см. в статье [Создание шаблонов](resource-group-authoring-templates.md).

<!---HONumber=July15_HO3-->