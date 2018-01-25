---
title: "Резервное копирование для Azure Stack | Документация Майкрософт"
description: "Выполните резервное копирование по требованию для Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 955b286967ca2bc8450e8988ec16c6a5c352aa8a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="back-up-azure-stack"></a>Резервное копирование для Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Выполните резервное копирование по требованию для Azure Stack. Если необходимо включить службу резервного копирования инфраструктуры, см. инструкции по [включению резервного копирования для Azure Stack на портале администрирования](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Средства Azure Stack содержат командлет **Start-AzSBackup**. Инструкции по установке средств см. в статье [Начало работы с PowerShell в Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).

## <a name="start-azure-stack-backup"></a>Запуск резервного копирования Azure Stack

Откройте Windows PowerShell из командной строки с повышенными привилегиями в среде управления для оператора и выполните следующие команды:

```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1

    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Подтверждение завершения резервного копирования на портале администрирования

1. Войдите на портал администрирования Azure Stack по адресу [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Выберите **Дополнительные службы** > **Резервное копирование инфраструктуры**. Выберите **Конфигурация** в колонке **Резервное копирование инфраструктуры**.
3. Найти **имя** и **дату завершения** резервного копирования в списке **доступных резервных копий**.
4. Убедитесь, что для **состояния** отображается значение **Выполнено**.

Вы можете также подтвердить завершение резервного копирования на портале администрирования. Перейдите на страницу `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`.

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о рабочем процессе по восстановлению после потери данных. См. дополнительные сведения по [восстановлению после масштабной потери данных](azure-stack-backup-recover-data.md).
