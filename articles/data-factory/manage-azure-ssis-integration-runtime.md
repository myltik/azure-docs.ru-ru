---
title: "Управление средой выполнения интеграции Azure SSIS | Документация Майкрософт"
description: "Узнайте, как повторно настроить среду выполнения интеграции в фабрике данных Azure после подготовки."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 780e5673b5ed159a7f170373d54fea7c1713a910
ms.contentlocale: ru-ru
ms.lasthandoff: 09/28/2017

---

# <a name="manage-an-azure-ssis-integration-runtime"></a>Управление средой выполнения интеграции Azure SSIS
В статье [Create an Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md) (Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure) показано, как создать среду выполнения интеграции Azure SSIS с помощью фабрики данных Azure. Эта статья дополняет ее, предоставляя информацию о том, как остановить, запустить, перенастроить или удалить среду выполнения интеграции Azure SSIS.  


## <a name="stop"></a>Остановить 
Остановите среду выполнения интеграции Azure SSIS. Эта команда освобождает все узлы и прекращает выставление счетов.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>Начало 
Запустите среду выполнения интеграции Azure SSIS. Эта команда выделяет все узлы и начинает выставление счетов.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>Перенастройка
После подготовки и запуска экземпляра среды выполнения Azure SSIS его можно перенастроить, выполнив последовательность командлетов PowerShell `Stop` - `Set` - `Start`. Например, следующий сценарий PowerShell изменяет количество узлов, выделенных для экземпляра среды выполнения интеграции Azure-SSIS, до 5.

1. Сначала остановите среду выполнения интеграции Azure SSIS, выполнив такую команду:

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Теперь измените масштаб среды выполнения интеграции Azure SSIS до пяти узлов.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Затем запустите среду выполнения интеграции Azure SSIS. Так будут выделены все ее узлы для запуска пакетов SSIS.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>Удалить
Чтобы удалить имеющуюся среду выполнения интеграции Azure SSIS, выполните следующую команду: 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о среде выполнения Azure SSIS см. в следующих разделах: 

- [Среда выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). В этой статье содержатся общие сведения о средах выполнения интеграции в целом, включая Azure SSIS IR. 
- [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-deploy-ssis-packages-azure.md). Эта статья содержит пошаговые инструкции для создания Azure SSIS IR и использует базу данных SQL Azure для размещения каталога SSIS. 
- [Создание среды выполнения интеграции Azure SSIS](create-azure-ssis-integration-runtime.md). Эта статья дополняет соответствующее руководство, а также предоставляет инструкции по использованию управляемого экземпляра SQL Azure (закрытая предварительная версия) и присоединению среды выполнения интеграции к виртуальной сети. 
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этом статье показано, как извлечь сведения о среде выполнения интеграции Azure SSIS и описания состояний из возвращаемых данных. 
- [Join an Azure-SSIS integration runtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети). В этой статье содержатся общие сведения о присоединении среды выполнения интеграции SSIS Azure к виртуальной сети Azure. В ней также показано, как настроить виртуальную сеть, чтобы присоединить среду выполнения интеграции SSIS Azure к виртуальной сети с помощью портала Azure. 

