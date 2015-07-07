<properties 
	pageTitle="Развертывание веб-приложения, связанного с репозиторием GitHub" 
	description="Используйте шаблон диспетчера ресурсов Azure для развертывания веб-приложения, содержащего проект из репозитория GitHub." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# Развертывание веб-приложения, связанного с репозиторием GitHub

В этой статье рассказывается, как создать шаблон диспетчера ресурсов Azure, выполняющий развертывание веб-приложения, которое связано с проектом в репозитории GitHub. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с конкретными требованиями.

Дополнительную информацию о создании шаблонов см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

Полная версия шаблона приведена в файле [Веб-приложение, связанное с шаблоном GitHub](https://github.com/tfitzmac/AppServiceTemplates/blob/master/WebAppLinkedToGithub.json).

## Что именно развертывается

С помощью этого шаблона можно развернуть веб-приложение, содержащее код проекта из GitHub.

## Параметры

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]
    
## Развертываемые ресурсы

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Веб-приложение

Создает веб-приложение, связанное с проектом в GitHub.

Имя веб-приложения задается с помощью параметра **siteName**, а его расположение — с помощью параметра **siteLocation**. В элементе **dependsOn** шаблон определяет зависимость веб-приложения от плана размещения служб. Поскольку веб-приложение зависит от плана размещения, оно не создается до тех пор, пока не завершено создание плана. Элемент **dependsOn** используется только для определения последовательности развертывания. Если веб-приложение не пометить как зависимое от плана размещения, диспетчер ресурсов Azure попытается создать оба ресурса одновременно, в результате чего может появиться сообщение об ошибке (в случае если веб-приложение будет создано до плана размещения).

У веб-приложения также есть дочерний ресурс, который определен в разделе **resources** ниже. Дочерний ресурс задает систему управления версиями для проекта, развертываемого с помощью веб-приложения. В этом шаблоне система управления версиями связана с определенным репозиторием GitHub. Репозиторий GitHub задается с помощью кода **"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"**. URL-адрес репозитория можно внести непосредственно в код, если необходимо создать шаблон, развертывающий один проект с минимальным набором параметров. Вместо этого также можно добавить параметр URL-адреса репозитория и использовать его значение для свойства **RepoUrl**. Пример параметра URL-адреса репозитория приведен в [шаблоне веб-приложения с веб-заданиями](../app-service-web-deploy-web-app-with-webjobs.md).

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Инфраструктура CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json


 

<!---HONumber=62-->