---
title: "Скрипт PowerShell. Преобразование данных в облаке с помощью фабрики данных | Документация Майкрософт"
description: "Этот скрипт PowerShell преобразовывает данные в облаке путем выполнения программы Spark на кластере Azure HDInsight Spark."
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 42135f0a0101ba82bca0d662ae4b970f799f70d3
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Сценарий PowerShell. Преобразование данных в облаке с помощью фабрики данных Azure

Этот пример сценария PowerShell создает конвейер, который преобразовывает данные в облаке путем выполнения программы Spark на кластере Azure HDInsight Spark. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта
> [!IMPORTANT]
> Этот скрипт создает JSON-файлы, определяющие сущности фабрики данных (связанную службу, набор данных и конвейер) на жестком диске в папке c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```
Чтобы удалить фабрику данных из группы ресурсов, выполните следующую команду: 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<data factory name>" -ResourceGroupName "<resource group name>"
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды:

| Команда | Примечания |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2) | Создадите фабрику данных. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedservice) | Создает в этой фабрике данных связанную службу. Связанная служба вычисляет или привязывает хранилище данных к фабрике данных. |
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactorv2ypipeline) | Создает конвейер в фабрике данных. Конвейер содержит одно или несколько действий, выполняющих определенную операцию. В этом конвейере действие Spark преобразовывает данные путем выполнения программы в кластере Azure HDInsight Spark. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2pipelinerun) | Создает выполнение для конвейера. Другими словами, запускает конвейер. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Получает сведения о выполнении действия в конвейере. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры скриптов PowerShell для базы данных Azure Cosmos DB можно найти [здесь](../samples-powershell.md).
