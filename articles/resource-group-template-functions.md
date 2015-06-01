<properties
   pageTitle="Функции шаблонов в диспетчере ресурсов Azure"
   description="В статье описаны функции в шаблоне диспетчера ресурсов Azure, которые можно использовать для развертывания приложений в Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Функции шаблонов в диспетчере ресурсов Azure

В этом разделе описаны все функции, которые можно использовать в шаблоне диспетчера ресурсов Azure.

## base64

**base64 (входная_строка)**

Возвращает входную строку в кодировке Base64.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| входная_строка | Да | Строковое значение, которое нужно вернуть в кодировке Base64.

В следующем примере показано, как использовать функцию Base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

## concat

**concat (аргумент1, аргумент2, аргумент3...)**

Объединяет несколько строковых значений и возвращает строковое значение. Эта функция может принимать любое количество аргументов.

В следующем примере показано объединение нескольких значений для возврата значения.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## listKeys

**listKeys (имя_ресурса или идентификатор_ресурса, [версия_API])**

Возвращает ключи учетной записи хранения. Параметр "идентификатор_ресурса" можно задать с помощью [функции resourceId](./#resourceid) или с использованием формата **пространство_имен_поставщика/тип_ресурса/имя_ресурса**. Вы можете использовать эту функцию для получения primaryKey и secondaryKey.
  
| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| имя_ресурса или идентификатор_ресурса | Да | Уникальный идентификатор учетной записи хранения.
| версия_API | Да | Версия API для состояния среды выполнения ресурса.

В следующем примере показано, как получить ключи из учетной записи хранения в разделе выходных данных.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## параметров

**parameters (имя_параметра)**

Возвращает значение параметра. Указанное имя параметра должно быть определено в разделе параметров шаблона.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| имя_параметра | Да | Имя параметра, который требуется вернуть.

В следующем примере показано упрощенное использование функции parameters.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

## provider

**provider (пространство_имен_поставщика, [тип_ресурса])**

Возвращает сведения о поставщике ресурсов и поддерживаемых им типах ресурсов. Если тип не указан, возвращаются все поддерживаемые типы.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| пространство_имен_поставщика | Да | Пространство имен поставщика.
| тип_ресурса | Нет | Тип ресурса в указанном пространстве имен.

Все поддерживаемые типы возвращаются в следующем формате:

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

Следующий пример показывает, как использовать функцию provider:

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

## reference

**reference (имя_ресурса или идентификатор_ресурса, [версия_API])**

Позволяет выражению получать значение из состояния среды выполнения другого ресурса.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| имя_ресурса или идентификатор_ресурса | Да | Имя или уникальный идентификатор ресурса.
| версия_API | Нет | Версия API для состояния среды выполнения ресурса. Этот параметр нужно использовать, если ресурс не предоставляется в рамках того же шаблона.

Функция **reference** получает свое значение из состояния среды выполнения, и поэтому ее невозможно использовать в разделе переменных. Она может использоваться в разделе выходных данных шаблона.

С помощью выражения reference вы объявляете, что один ресурс зависит от другого, если ресурс, на который указывает ссылка, предоставляется в том же шаблоне.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## resourceGroup

**resourceGroup()**

Возвращает структурированный объект, представляющий текущую группу ресурсов. Формат объекта будет следующим:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

В следующем примере расположение группы ресурсов используется для назначения расположения веб-сайту.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

## resourceId

**resourceId ([имя_группы_ресурсов], тип_ресурса, имя_ресурса1, [имя_ресурса2]...)**

Возвращает уникальный идентификатор ресурса. Используйте эту функцию в том случае, когда имя ресурса является неоднозначным или не было предоставлено в пределах того же шаблона. Идентификатор возвращается в следующем формате:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Параметр | Обязательно | Описание
| :---------------: | :------: | :----------
| имя_группы_ресурсов | Нет | Необязательное имя группы ресурсов. Значение по умолчанию — текущая группа ресурсов. Укажите это значение, если вы получаете ресурс из другой группы ресурсов.
| тип_ресурса | Да | Тип ресурса, включая пространство имен поставщика ресурсов.
| имя_ресурса1 | Да | Имя ресурса.
| имя_ресурса2 | Нет | Имя следующего ресурса, если ресурс является вложенным.

В следующем примере показано, как получить идентификаторы ресурсов для веб-сайта и базы данных. Веб-сайт находится в группе ресурсов с именем **myWebsitesGroup**, а база данных — в текущей группе ресурсов для этого шаблона.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
Эта функция часто необходима при использовании учетной записи хранения или виртуальной сети в альтернативной группе ресурсов. Учетная запись хранения и виртуальная сеть могут использоваться несколькими группами ресурсов, поэтому их не следует удалять при удалении одной группы ресурсов. В следующем примере показано, как можно легко использовать ресурс из внешней группы ресурсов:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }


## подписку

**subscription()**

Возвращает сведения о подписке в следующем формате:

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

В следующем примере показана функция subscription, вызываемая в разделе выходных данных.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 

## variables

**variables (имя_переменной)**

Возвращает значение переменной. Указанное имя переменной должно быть определено в разделе переменных шаблона.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| имя_переменной | Да | Имя переменной, которую необходимо вернуть.


## Дальнейшие действия
- [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md)
- [Расширенные операции с шаблонами](./resource-group-advanced-template.md)
- [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](./resouce-group-template-deploy.md)
- [Общие сведения о диспетчере ресурсов Azure](./resource-group-overview.md)

<!--HONumber=52-->
