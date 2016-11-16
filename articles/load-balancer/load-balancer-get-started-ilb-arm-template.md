---
title: "Создание подсистемы балансировки нагрузки в Resource Manager с помощью шаблона | Документация Майкрософт"
description: "Узнайте, как создать внутренний балансировщик нагрузки в диспетчере ресурсов с помощью шаблона."
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 64150862-6ced-42de-85dc-89d323257d7c
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 482c9cd46902d9e3f4e1e0f001182fdb43ce9367


---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Создайте внутренний балансировщик нагрузки с помощью шаблона
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

<BR>
[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[Классическая модель развертывания](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Развертывание шаблона с помощью кнопки развертывания
Образец шаблона, который находится в общедоступном репозитории, использует файл параметров, содержащий значения по умолчанию для создания описанного выше сценария. Чтобы развернуть этот шаблон, перейдите по [данной ссылке](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), нажмите **Deploy to Azure**(Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

## <a name="deploy-the-template-by-using-powershell"></a>Развертывание шаблона с помощью PowerShell
Чтобы развернуть шаблон, загруженный с помощью PowerShell, выполните описанные ниже действия.

1. Если вы ранее не использовали Azure PowerShell, следуйте указаниям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md) до этапа входа в Azure и выбора подписки.
2. Скачайте файл параметров на локальный диск.
3. Измените и сохраните файл.
4. Выполните командлет **New-AzureRmResourceGroupDeployment** , чтобы создать группу ресурсов с помощью шаблона.
   
        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Развертывание шаблона с помощью интерфейса командной строки Azure
Чтобы развернуть шаблон с помощью интерфейса командной строки Azure, выполните следующие действия.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в режим диспетчера ресурсов, как показано ниже.
   
        azure config mode arm
   
    Вот результат, ожидаемый для указанной выше команды:
   
        info:    New mode is arm
3. Откройте файл параметров, выберите его содержимое и сохраните его в файл на своем компьютере. В этом примере мы сохранили файл параметров в файл *parameters.json*.
4. Выполните команду **azure group deployment create** , чтобы развернуть новый внутренний балансировщик нагрузки с помощью данного шаблона и файлов параметров, которые вы скачали и изменили ранее. В списке, который откроется после выполнения команды, будут указаны используемые параметры.
   
        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Дальнейшие действия
[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


