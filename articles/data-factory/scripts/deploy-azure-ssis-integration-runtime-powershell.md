---
title: "Скрипт PowerShell. Развертывание среды выполнения интеграции Azure SSIS | Документация Майкрософт"
description: "Этот скрипт PowerShell создает среду выполнения интеграции Azure SSIS, которая может запускать пакеты SSIS в облаке."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.openlocfilehash: 9be0ed15f5bea06b499c696a63829f7898eb7b74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>Скрипт PowerShell. Развертывание среды выполнения интеграции Azure SSIS

В этом примере сценария PowerShell создается среда выполнения интеграции Azure SSIS, которая может запускать пакеты SSIS в Azure.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды:

| Команда | Примечания |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Создадите фабрику данных. |
| [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime) | Создает среду выполнения интеграции Azure SSIS, которая может запускать пакеты SSIS в облаке. |
| [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime) | Запускает среду выполнения интеграции Azure SSIS. |
| [Get-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntime) | Получает сведения о среде выполнения интеграции Azure SSIS. |
| [Get-AzureRmDataFactoryV2IntegrationRuntimeStatus](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntimestatus) | Получает сведения о состоянии среды выполнения интеграции Azure SSIS. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры скриптов PowerShell для базы данных Azure Cosmos DB можно найти [здесь](../samples-powershell.md).