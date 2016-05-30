<properties 
	pageTitle="Привязка ресурсов в диспетчере ресурсов Azure | Microsoft Azure" 
	description="Создавайте связи между взаимосвязанными ресурсами в разных группах ресурсов в Azure Resource Manager." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2016" 
	ms.author="tomfitz"/>

# Привязка ресурсов в диспетчере ресурсов Azure

Во время развертывания можно пометить ресурс как зависимый от другого ресурса, но во время развертывания этот жизненный цикл завершается. После завершения развертывания идентифицируемой связи между зависимыми ресурсами не существует. Resource Manager предоставляет функцию для установления сохраняемых связей между ресурсами, которая называется привязкой ресурсов.

С помощью привязки ресурсов можно сохранять информацию о связях между группами ресурсов. Например, база данных с определенным жизненным циклом может находиться в одной группе ресурсов, а приложение с иным жизненным циклом — в другой. Приложение подключается к базе данных, поэтому требуется пометить связь между приложением и этой базой данных.

Все связанные ресурсы должны принадлежать к одной подписке. Каждый ресурс может быть связан с 50 другими ресурсами. Единственный способ запросить связанные ресурсы — через REST API. При удалении или перемещении каких-либо связанных ресурсов владельцу связи нужно удалить оставшуюся связь. При удалении ресурса, связанного с другими ресурсами, предупреждение **не** отображается.

## Привязка с использованием шаблонов

Чтобы определить связь в шаблоне, включите тип ресурса, который объединяет пространство имен поставщика ресурсов и тип исходного ресурса с **/providers/links**. Имя должно включать в себя имя исходного ресурса. Укажите идентификатор ресурса для целевого ресурса. В следующем примере показана установка связи между веб-сайтом и учетной записью хранения.

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


Полное описание формата шаблона см. в разделе [Связи ресурсов — схема шаблона](resource-manager-template-links.md).

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

Можно выполнить запрос связей в подписке с помощью следующей ссылки:

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Дополнительные примеры, в том числе способы получения информации о связях, см. в разделе [Связанные ресурсы](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Дальнейшие действия

- Вы также можете организовать ресурсы с помощью тегов. Дополнительную информацию о маркировке ресурсов тегами см. в статье [Использование тегов для организации ресурсов](resource-group-using-tags.md).
- Описание способов создания шаблонов и определения ресурсов для развертывания см. в статье [Создание шаблонов](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0518_2016-->