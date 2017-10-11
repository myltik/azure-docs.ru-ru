---
title: "Удаление инструмента заданий обработки эластичных баз данных"
description: "Удаление инструмента заданий обработки эластичных баз данных"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: bfc9d820-edbd-4fca-bfbf-1f339cfcc448
ms.service: sql-database
ms.workload: sql-database
ms.custom: scale out apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: ae7f0bce452a0a86f6f1e4d9b0c93a0fa1727f21
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="uninstall-elastic-database-jobs-components"></a>Удаление компонентов заданий обработки эластичных баз данных.
**заданий обработки эластичных баз данных** можно удалить с помощью портала или PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Удаление компонентов заданий обработки эластичных баз данных с помощью портала Azure
1. Откройте [портал Azure](https://portal.azure.com/).
2. Перейдите к подписке, которая содержит компоненты **заданий обработки эластичных баз данных** , то есть подписку, в которой они были установлены.
3. Щелкните **Обзор** и **Группы ресурсов**.
4. Выберите группу ресурсов с именем __ElasticDatabaseJob.
5. Удалите ее.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Удаление компонентов заданий обработки эластичных баз данных с помощью PowerShell
1. Откройте командную строку Microsoft Azure PowerShell и перейдите в подкаталог tools в папке Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: введите команду **cd tools**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. Запустите сценарий PowerShell .\UninstallElasticDatabaseJobs.ps1.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

Или просто запустите следующий сценарий, если для установки компонентов использовались значения по умолчанию:

        $ResourceGroupName = "__ElasticDatabaseJob"
        Switch-AzureMode AzureResourceManager

        $resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
        if(!$resourceGroup)
        {
            Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
            return
        }

        Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
        Remove-AzureResourceGroup -Name $ResourceGroupName -Force
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>Дальнейшие действия
Сведения о повторной установке службы можно найти в статье [Установка компонентов службы заданий обработки эластичных баз данных](sql-database-elastic-jobs-service-installation.md)

Общие сведения о заданиях обработки эластичных баз данных см. в статье [Управление масштабируемыми облачными базами данных](sql-database-elastic-jobs-overview.md).

<!--Image references-->


