---
title: "Автоматизация развертывания ресурсов для приложения Функций Azure | Документация Майкрософт"
description: "Узнайте, как создать шаблон Azure Resource Manager, позволяющий развертывать приложения Функций Azure."
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "функции Azure, функции, независимая от сервера архитектура, инфраструктура как код, Azure Resource Manager"
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>Автоматизация развертывания ресурсов для приложения Функций Azure

Для развертывания приложения Функций Azure можно использовать шаблон Azure Resource Manager. Узнайте, как определить базовые ресурсы, необходимые для приложения Функций Azure, и параметры, которые задаются при развертывании. В зависимости от [триггеров и привязок](functions-triggers-bindings.md) в приложении Функций может потребоваться развернуть дополнительные ресурсы, чтобы создать успешную конфигурацию инфраструктуры как кода для приложения.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Чтобы изучить примеры полных шаблонов, ознакомьтесь с [созданием приложения Функций Azure на основе плана потребления](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) и [созданием приложения Функций Azure на основе плана службы приложений](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json).

## <a name="required-resources"></a>Необходимые ресурсы

Для создания базового приложения Функций Azure можно воспользоваться приведенными в этой статье примерами. Для приложения вам понадобятся следующие ресурсы:

* [учетная запись хранения Azure](../storage/index.md);
* план размещения (план потребления или план службы приложений Azure);
* приложение Функций (`type`: **Microsoft.Web/Site**, `kind`: **functionapp**).

## <a name="parameters"></a>Параметры

С помощью Azure Resource Manager можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В разделе **Параметры** шаблона содержатся все значения параметров. Определите параметры для значений, которые зависят от развертываемого проекта или среды, в которой выполняется развертывание.

[Переменные](../azure-resource-manager/resource-group-authoring-templates.md#variables) удобно использовать для значений, которые не изменяются для каждого развертывания, а также для параметров, которые требуется преобразовать, прежде чем использовать в шаблоне (например, для передачи правил проверки).

При определении параметров с помощью поля **allowedValues** задайте значения, указываемые пользователем во время развертывания. С помощью поля **defaultValue** назначьте параметру значение, которое будет использоваться в случае, если во время развертывания значение не указано.

В шаблоне Azure Resource Manager используются следующие параметры.

### <a name="appname"></a>appName

Имя приложения Функций Azure, которое необходимо создать.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Расположение для развертывания приложения Функций.

> [!NOTE]
> Используйте параметр **defaultValue** для наследования расположения группы ресурсов или в случае, если значение параметра не указано во время развертывания с помощью PowerShell или интерфейса командной строки Azure. При развертывании приложения с портала Azure выберите значение в окне раскрывающегося списка параметров **allowedValues**.

> [!TIP]
> Текущий список регионов, в которых можно использовать Функции Azure, доступен на странице [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/).

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL (необязательно)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch (необязательно)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (необязательно)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>Переменные

Шаблоны Azure Resource Manager используют переменные для включения параметров, поэтому в шаблоне можно выполнить более точные настройки.

В следующем примере в соответствии с [требованиями к именованию](../storage/storage-create-storage-account.md#create-a-storage-account) учетной записи хранения Azure используются переменные, чтобы применить [функции шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) для преобразования введенного значения **appName** в нижний регистр.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Развертываемые ресурсы

### <a name="storage-account"></a>Учетная запись хранения

Учетная запись хранения Azure — обязательный ресурс для приложения Функций Azure.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service"></a>План размещения: потребление или Служба приложений

В некоторых сценариях может потребоваться, чтобы функции масштабировались платформой по запросу. Этот подход также называют полностью управляемым масштабированием (размещение на основе плана потребления). Другой вариант — выбрать управляемое пользователем масштабирование функций. При этом подходе функции круглосуточно выполняются на выделенном оборудовании (размещение на основе плана службы приложений). Число экземпляров можно настроить вручную или автоматически. Выбор плана размещения может основываться на доступных возможностях плана или затратах на соответствующую архитектуру. Дополнительные сведения о планах размещения см. в статье [Масштабирование Функций Azure](functions-scale.md).

#### <a name="consumption-plan"></a>План потребления

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>План службы приложений

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="functions-app-a-site"></a>Приложение Функций (сайт)

Выбрав вариант масштабирования, создайте приложение Функций. Приложение является контейнером, в котором содержатся все функции.

Приложение Функций содержит много дочерних ресурсов, которые можно использовать при развертывании, в том числе параметры приложения и параметры системы управления версиями. Вы можете также удалить дочерний ресурс **sourcecontrols** и выбрать другой [вариант развертывания](functions-continuous-deployment.md).

> [!IMPORTANT]
> Чтобы с помощью Azure Resource Manager создать успешную конфигурацию инфраструктуры как кода для приложения, важно понимать, каким образом ресурсы развертываются в Azure. В следующем примере конфигурации верхнего уровня применяются с помощью **siteConfig**. Их важно задать на верхнем уровне, так как эти конфигурации передают сведения в среду выполнения и механизм развертывания Функций Azure. Сведения верхнего уровня требуются перед применением дочернего ресурса **sourcecontrols/web**. Хотя эти параметры можно настроить в дочернем ресурсе **config/appSettings**, в некоторых сценариях приложение Функций и функции требуется развернуть *до* применения **config/appSettings**. В таких случаях, например в [Logic Apps](../logic-apps/index.md), функции зависят от другого ресурса.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> В этом шаблоне используется значение параметров приложения [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), задающее базовый каталог, в котором подсистема развертывания функций (Kudu) ищет развертываемый код. В нашем репозитории функции находятся во вложенной папке папки **src**. Таким образом, в предыдущем примере мы задаем для параметров приложения значение `src`. Если ваши функции находятся в корневой папке репозитория, или если развертывание выполняется не из системы управления версиями, то это значение параметров приложения можно удалить.

## <a name="deploy-your-template"></a>Развертывание шаблона

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Интерфейс командной строки Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Портал Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [ИНТЕРФЕЙС REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Кнопка "Развертывание в Azure"

Замените ```<url-encoded-path-to-azuredeploy-json>``` версией необработанного пути к файлу `azuredeploy.json` на сайте GitHub, указав его в формате [URL-адреса](https://www.bing.com/search?q=url+encode).

#### <a name="markdown"></a>Разметка Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке и настройке Функций Azure:

* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Настройка параметров приложения-функции Azure](functions-how-to-use-azure-function-app-settings.md)
* [Создание первой функции Azure](functions-create-first-azure-function.md)



<!--HONumber=Feb17_HO1-->


