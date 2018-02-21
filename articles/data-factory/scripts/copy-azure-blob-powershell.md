---
title: "Скрипт PowerShell: копирование данных в облаке с помощью фабрики данных Azure | Документация Майкрософт"
description: "С помощью скрипта PowerShell можно копировать данные из одного расположения в другое в хранилище BLOB-объектов Azure."
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
ms.openlocfilehash: c564491e0f30e0e898bc03eecb29710edeb267f6
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2017
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Создание конвейера фабрики данных для копирования данных в облаке с помощью PowerShell

Этот пример сценария PowerShell создает конвейер в фабрике данных Azure, который копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>предварительным требованиям
* **Учетная запись хранения Azure.** Хранилище BLOB-объектов используется как хранилища данных, являющиеся **источником** и **приемником**. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом о [создании учетной записи хранения](../../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Создайте **контейнер больших двоичных объектов** в хранилище BLOB-объектов, в контейнере создайте входную **папку** и отправьте несколько файлов в нее. Такие средства, как [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), можно использовать для подключения к хранилищу BLOB-объектов Azure, создания контейнера BLOB-объектов, отправки входного файла и проверки выходного.

## <a name="sample-script"></a>Пример скрипта

> [!IMPORTANT]
> Этот скрипт создает JSON-файлы, определяющие сущности фабрики данных (связанную службу, набор данных и конвейер) на жестком диске в папке c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Чтобы удалить фабрику данных из группы ресурсов, выполните следующую команду: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Заметки |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | создадите фабрику данных; |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Создает в этой фабрике данных связанную службу. Связанная служба вычисляет или привязывает хранилище данных к фабрике данных. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Создает набор данных в фабрике данных. Набор данных представляет ввод или вывод для действия в конвейере. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Создает конвейер в фабрике данных. Конвейер содержит одно или несколько действий, выполняющих определенную операцию. В этом конвейере действие копирования копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Создает выполнение для конвейера. Другими словами, запускает конвейер. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Получает сведения о выполнении действия в конвейере. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для Azure Data Factory приведены [здесь](../samples-powershell.md).