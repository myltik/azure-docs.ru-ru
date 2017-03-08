---
title: "Высокая плотность размещения в службе приложений Azure | Документация Майкрософт"
description: "Высокая плотность размещения в службе приложений Azure"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/11/2017
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0c2677b388f7a88ff88715a05212633565393cc2
ms.openlocfilehash: 2d5d1d5123ca718b2e7dcdf426b77f91969dc9dc
ms.lasthandoff: 01/13/2017


---
# <a name="high-density-hosting-on-azure-app-service"></a>Высокая плотность размещения в службе приложений Azure
При использовании службы приложений приложение отвязывается от выделяемой для него емкости двумя концепциями:

* **Приложение**. Представляет приложение и конфигурацию его среды выполнения. Например, оно включает версию .NET, которую должна загружать среда выполнения, а также параметры приложения и т. д.
* **План службы приложений**. Определяет характеристики емкости, доступный набор функций и расположение приложения. Это могут быть следующие характеристики: большие (четырехъядерные) компьютеры, четыре экземпляра или функции уровня "Премиум" в восточной части США.

Приложение всегда связано с одним планом службы приложений, но план службы приложений может предоставлять емкость для одного или нескольких приложений.

В результате платформа позволяет не только изолировать одно приложение, но и настроить несколько приложений так, чтобы они использовали один и тот же план службы приложений и одни и те же ресурсы.

Однако если несколько приложений совместно используют план службы приложений, один экземпляр этого приложения будет выполняться на каждом экземпляре данного плана службы приложений.

## <a name="per-app-scaling"></a>Независимое масштабирование приложений
*Независимое масштабирование приложений* — это функция, которую можно включить на уровне плана службы приложений и затем использовать для каждого приложения.

Она позволяет масштабировать приложение независимо от плана службы приложений, в котором оно размещено. Таким образом, план службы приложений может быть настроен на предоставление 10 экземпляров, а приложение можно настроить, например, на масштабирование только до 5 из них.

   >[!NOTE]
   >Независимое масштабирование приложений доступно только для планов службы приложений с номером SKU уровня **Премиум**.
   >

### <a name="per-app-scaling-using-powershell"></a>Независимое масштабирование приложений с помощью PowerShell

Вы можете создать план, настроенный в качестве плана с *независимым масштабированием приложений*, передав атрибут ```-perSiteScaling $true``` в командлет ```New-AzureRmAppServicePlan```.

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Если вы хотите обновить существующий план службы приложений для использования этой функции: 

- получите целевой план (```Get-AzureRmAppServicePlan```);
- измените свойство локально (```$newASP.PerSiteScaling = $true```);
- опубликуйте изменения в Azure (```Set-AzureRmAppServicePlan```). 

```
    # Get the new App Service Plan and modify the "PerSiteScaling" property.
    $newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
    $newASP

    #Modify the local copy to use "PerSiteScaling" property.
    $newASP.PerSiteScaling = $true
    $newASP
    
    #Post updated app service plan back to azure
    Set-AzureRmAppServicePlan $newASP
```

Создав и настроив план, вы можете задать максимальное количество экземпляров для каждого из приложений.

В следующем примере для приложения установлено ограничение на два экземпляра независимо от того, сколько экземпляров можно развернуть в пределах базового плана службы приложений.

```
    # Get the app we want to configure to use "PerSiteScaling"
    $newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
    # Modify the NumberOfWorkers setting to the desired value.
    $newapp.SiteConfig.NumberOfWorkers = 2
    
    # Post updated app back to azure
    Set-AzureRmWebApp $newapp
```

### <a name="per-app-scaling-using-azure-resource-manager"></a>Независимое масштабирование приложений с помощью Azure Resource Manager

Следующий *шаблон Azure Resource Manager* создает:

- план службы приложений, в пределах которого можно развернуть до 10 экземпляров;
- приложение, для которого настроено масштабирование до пяти экземпляров максимум.

План службы приложений задает для свойства **PerSiteScaling** значение true (```"perSiteScaling": true```). Приложение задает **количество рабочих ролей**, равное 5 (```"properties": { "numberOfWorkers": "5" }```).

```
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "P1",
                "tier": "Premium",
                "size": "P1",
                "family": "P",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Рекомендуемая конфигурация для высокой плотности размещения
Независимое масштабирование приложений — это функция, которая включается как в общедоступных регионах Azure, так и в средах службы приложений. Однако рекомендуется использовать среды службы приложений, так как они предоставляют дополнительные возможности и пулы большей емкости.  

Чтобы настроить высокую плотность размещения приложений, выполните следующие действия:

1. Настройте среду службы приложений и выберите рабочий пул, выделенный для сценария высокой плотности размещения.
2. Создайте один план службы приложений и настройте его для использования всей доступной емкости в рабочем пуле.
3. Задайте для флага PerSiteScaling плана службы приложений значение true.
4. Новые приложения будут созданы и назначены этому плану службы приложений, при этом для свойства **numberOfWorkers** будет задано значение **1**. Такая конфигурация позволит достичь максимальной плотности в этом рабочем пуле.
5. Число рабочих ролей можно настроить отдельно для каждого приложения, чтобы предоставить дополнительные ресурсы согласно требованиям. Например, для приложения с высоким уровнем использования свойству **numberOfWorkers** можно присвоить значение **3**, чтобы обеспечить большую вычислительную мощность для этого приложения, а для приложений с низким уровнем использования свойству **numberOfWorkers** можно присвоить значение **1**.


