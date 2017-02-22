---
title: "Автоматизация развертывания ресурсов для Функций Azure | Документация Майкрософт"
description: "Узнайте, как создать шаблон Azure Resource Manager, позволяющий развертывать приложения-функции в Microsoft Azure."
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
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>Автоматизация развертывания ресурсов для приложения-функции Azure

В этом разделе рассказывается, как создать шаблон Azure Resource Manager, выполняющий развертывание приложения-функции. Вы узнаете, как определить базовые ресурсы, необходимые для функции Azure, и параметры, которые задаются при развертывании. В зависимости от [триггеров и привязок](functions-triggers-bindings.md), используемых в функции, вам может потребоваться развернуть дополнительные ресурсы, чтобы обеспечить инфраструктуру как код для всего своего приложения.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Чтобы изучить примеры полных шаблонов, ознакомьтесь с [созданием функции Azure на основе потребления](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) и (или) [созданием плана службы приложений на основе функции Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json).

## <a name="what-is-deployed"></a>Развертываемые компоненты

В приведенных ниже примерах создается базовое приложение-функция Azure. Ниже приведены ресурсы, необходимые для приложения-функции:

* учетная запись [хранения Azure](../storage/index.md);
* план размещения (план потребления или план службы приложений);
* приложение-функция (Microsoft.Web/Site типа **functionapp**).

## <a name="parameters"></a>Параметры

С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел Parameters, содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо среды, в которой выполняется развертывание.

[Переменные](../azure-resource-manager/resource-group-authoring-templates.md#variables) удобно использовать для значений, которые не изменяются для каждого развертывания, или параметров, которые требуется преобразовать, прежде чем использовать в шаблоне (например, для передачи правил проверки).

При определении параметров задать значения, указываемые пользователем во время развертывания, можно с помощью поля **allowedValues** . С помощью поля **defaultValue** можно назначить параметру значение, которое будет использоваться в случае, если значение во время развертывания не указано.

Давайте опишем параметры для нашего шаблона.

### <a name="appname"></a>appName

Имя функции, которую требуется создать.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Расположение, в котором будет развернуто приложение-функция.

> [!NOTE]
> Параметр **DefaultValue** используется для наследования расположения группы ресурсов или в случае, если значение параметра не указано во время развертывания с помощью PowerShell или командной строки. При развертывании с портала доступен раскрывающийся список для выбора одного из значений **allowedValues**.

> [!TIP]
> Текущий список регионов и функций Azure доступен на странице [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/).

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
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (необязательно)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>Переменные

Помимо параметров в шаблоне Azure Resource Manager также используется понятие переменных. Переменные могут содержать параметры, что позволяет более точно настроить шаблон.

В приведенном ниже примере показано, как с помощью переменных [функции шаблона Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) используются для получения и преобразования значения appName в нижний регистр, чтобы обеспечить соблюдение [требований к именованию](../storage/storage-create-storage-account.md#create-a-storage-account) для учетных записей хранения Azure.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Развертываемые ресурсы

### <a name="storage-account"></a>Учетная запись хранения

Учетная запись хранения Azure — обязательный ресурс в Функциях Azure.

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

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>План размещения: план потребления или план службы приложений

Существуют сценарии, в которых создаются функции, для которых может понадобиться полностью управляемое масштабирование, то есть масштабирование платформой по запросу (модель потребления). Кроме того, можно выбрать управляемое пользователем масштабирование, при котором функции круглосуточно выполняются на выделенном оборудовании (план службы приложений), на котором число экземпляров может быть настроено вручную или автоматически. Выбор плана может основываться на доступных возможностях плана или затратах на соответствующую архитектуру. Чтобы узнать больше о различных планах размещения, прочитайте статью [Масштабирование Функций Azure](functions-scale.md).

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

### <a name="function-app-site"></a>Приложение-функция (сайт)

После выбора варианта масштабирования следует создать контейнер, который будет содержать все ваши функции, который называется приложением-функцией.

Приложение-функция содержит много дочерних ресурсов, преимущества которых можно использовать, в том числе **параметры приложения** и **параметры системы управления версиями**. Вы можете удалить дочерний ресурс **sourcecontrols** и выбрать другой [вариант развертывания](functions-continuous-deployment.md).

> [!IMPORTANT]
> Важно понимать, каким образом ресурсы развертываются в Azure, чтобы создать успешную конфигурацию инфраструктуры как кода для приложения при использовании Azure Resource Manager. В этом примере можно видеть, что конфигурации верхнего уровня применяются с помощью **siteConfig**. Их важно задать на верхнем уровне, так как эти конфигурации передают значение в среду выполнения и механизм развертывания Функций Azure, а это требуется выполнить перед применением дочернего ресурса **sourcecontrols/web**. Хотя эти параметры можно настроить в дочернем ресурсе **config/appSettings**, существуют сценарии, в которых приложение-функцию и функции потребуется развернуть *перед* применением **config/appsettings**, так как ваши функции зависят от другого ресурса, например [приложения логики](../logic-apps/index.md).

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
> В этом шаблоне используется параметр приложения [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), задающий базовый каталог, в котором подсистема развертывания функций (Kudu) будет искать развертываемый код. В этом примере мы присвоили этому параметру значение `src`, так как наш репозиторий GitHub содержит папку `src`, в которой содержатся дочерние функции. Если в вашем репозитории функции расположены в корневой папке или развертывание выполняется не из системы управления версиями, то этот параметр приложения можно удалить.

## <a name="deploying-your-template"></a>Развертывание шаблона

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Портал](../azure-resource-manager/resource-group-template-deploy-portal.md)
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

Теперь, когда вы можете развернуть приложение-функцию из кода, воспользуйтесь возможностью получить дополнительные сведения о разработке и настройке Функций Azure.

* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Настройка параметров приложения-функции Azure](functions-how-to-use-azure-function-app-settings.md)
* [Создание первой функции Azure](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


