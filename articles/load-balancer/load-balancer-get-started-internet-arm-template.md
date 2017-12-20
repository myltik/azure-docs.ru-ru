---
title: "Создание доступной в Интернете внутренней подсистемы балансировки нагрузки с помощью шаблона Azure | Документация Майкрософт"
description: "Узнайте, как создать балансировщик нагрузки для Интернета в диспетчере ресурсов с помощью шаблона"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 10d96920d3aa3cae19750d7c2437b88bac1c8cda
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>Создание балансировщика нагрузки для Интернета с помощью шаблона

> [!div class="op_single_selector"]
> * [Портал](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Шаблон](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Развертывание шаблона с помощью кнопки развертывания

Образец шаблона, который находится в общедоступном репозитории, использует файл параметров, содержащий значения по умолчанию для создания описанного выше сценария. Чтобы развернуть этот шаблон, перейдите по [данной ссылке](http://go.microsoft.com/fwlink/?LinkId=544801), нажмите **Deploy to Azure**(Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

## <a name="deploy-the-template-by-using-powershell"></a>Развертывание шаблона с помощью PowerShell

Чтобы развернуть шаблон, загруженный с помощью PowerShell, выполните описанные ниже действия.

1. Если вы ранее не использовали Azure PowerShell, следуйте указаниям в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview) до этапа входа в Azure и выбора подписки.
2. Выполните командлет **New-AzureRmResourceGroupDeployment** , чтобы создать группу ресурсов с помощью шаблона.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Развертывание шаблона с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон с помощью интерфейса командной строки Azure, выполните следующие действия.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

    ```azurecli
    azure config mode arm
    ```

    Вот результат, ожидаемый для указанной выше команды:

        info:    New mode is arm

3. В браузере перейдите к [шаблону быстрого запуска](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), скопируйте содержимое JSON-файла и вставьте его в новый файл на своем компьютере. В данном сценарии скопируйте указанные ниже значения в файл **c:\lb\azuredeploy.parameters.json**.
4. Выполните командлет **azure group deployment create** , чтобы развернуть новый балансировщик нагрузки с помощью данного шаблона и файлов параметров, которые вы скачали и изменили ранее. В списке, который откроется после выполнения команды, будут указаны используемые параметры.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
