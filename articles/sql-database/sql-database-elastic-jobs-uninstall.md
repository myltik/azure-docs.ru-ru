---
title: Удаление инструмента заданий обработки эластичных баз данных
description: Узнайте, как удалять компоненты заданий обработки эластичных баз данных с помощью портала Azure или PowerShell.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: f5d0579cbb5f787ce08e2a2cea58d3c39a967970
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
---
# <a name="uninstall-elastic-database-jobs-components"></a>Удаление компонентов заданий обработки эластичных баз данных.
**Задания обработки эластичных баз данных** можно удалить с помощью портала Azure или PowerShell.

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

## <a name="next-steps"></a>Дополнительная информация
Сведения о повторной установке службы можно найти в статье [Установка компонентов службы заданий обработки эластичных баз данных](sql-database-elastic-jobs-service-installation.md)

Общие сведения о заданиях обработки эластичных баз данных см. в статье [Управление масштабируемыми облачными базами данных](sql-database-elastic-jobs-overview.md).

<!--Image references-->


