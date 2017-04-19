---
title: "Развертывание веб-приложения, связанного с репозиторием GitHub | Документация Майкрософт"
description: "Используйте шаблон диспетчера ресурсов Azure для развертывания веб-приложения, содержащего проект из репозитория GitHub."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 32739607-85fe-43c8-a4dc-1feb46d93a4d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 92408d6358516cdb2cea068553757c036143e955
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Развертывание веб-приложения, связанного с репозиторием GitHub
В этом разделе рассказывается, как создать шаблон диспетчера ресурсов Azure, выполняющий развертывание веб-приложения, которое связано с проектом в репозитории GitHub. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Полная версия шаблона приведена в файле [Веб-приложение, связанное с шаблоном GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-deploy"></a>Что именно развертывается
С помощью этого шаблона можно развернуть веб-приложение, содержащее код проекта из GitHub.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL
URL-адрес репозитория GitHub, в котором расположен развертываемый проект. Этот параметр содержит значение по умолчанию, которое лишь показывает, как указывать URL-адрес для репозитория. Это значение можно использовать при проверке шаблона, но при работе с ним вам нужно будет указать URL-адрес своего репозитория.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>ветвь
Ветвь репозитория для развертывания приложения. По умолчанию указана основная ветвь, но можно указать любую другую ветвь репозитория, из которой вы хотите развернуть приложение.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }

## <a name="resources-to-deploy"></a>Развертываемые ресурсы
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Веб-приложение
Создает веб-приложение, связанное с проектом в GitHub. 

Имя веб-приложения задается с помощью параметра **siteName**, а его расположение — с помощью параметра **siteLocation**. В элементе **dependsOn** шаблон определяет зависимость веб-приложения от плана размещения служб. Поскольку веб-приложение зависит от плана размещения, оно не создается до тех пор, пока не завершено создание плана. Элемент **dependsOn** используется только для определения последовательности развертывания. Если веб-приложение не пометить как зависимое от плана размещения, диспетчер ресурсов Azure попытается создать оба ресурса одновременно, в результате чего может появиться сообщение об ошибке (в случае если веб-приложение будет создано до плана размещения).

У веб-приложения также есть дочерний ресурс, определенный в разделе **resources** ниже. Дочерний ресурс задает систему управления версиями для проекта, развертываемого с помощью веб-приложения. В этом шаблоне система управления версиями связана с определенным репозиторием GitHub. Репозиторий GitHub задается с помощью кода **"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"**. URL-адрес репозитория можно жестко задать, если необходимо создать шаблон, развертывающий один проект с минимальным набором параметров.
Вместо этого также можно добавить параметр URL-адреса репозитория и использовать его значение для свойства **RepoUrl**.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Инфраструктура CLI Azure

    azure group deployment create -g {resource-group-name} --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json

### <a name="azure-cli-20"></a>Azure CLI 2.0

    az group deployment create -g {resource-group-name} --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json --parameters '@azuredeploy.parameters.json'

> [!NOTE] 
> Содержимое JSON-файла параметров см. в [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.parameters.json).
>
>


