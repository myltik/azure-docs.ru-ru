---
title: Резервное копирование для Azure Stack | Документация Майкрософт
description: Выполните резервное копирование по требованию для Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075193"
---
# <a name="back-up-azure-stack"></a>Резервное копирование для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Выполните резервное копирование по требованию для Azure Stack. Если необходимо включить службу резервного копирования инфраструктуры, см. инструкции по [включению резервного копирования для Azure Stack на портале администрирования](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Инструкции по настройке среды PowerShell приведены в разделе [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md).

## <a name="start-azure-stack-backup"></a>Запуск резервного копирования Azure Stack

Откройте Windows PowerShell из командной строки с повышенными привилегиями в среде управления для оператора и выполните следующие команды:

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Подтверждение завершения резервного копирования на портале администрирования

1. Откройте портал администрирования Azure Stack по адресу: [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Выберите **Дополнительные службы** > **Резервное копирование инфраструктуры**. Выберите **Конфигурация** в колонке **Резервное копирование инфраструктуры**.
3. Найти **имя** и **дату завершения** резервного копирования в списке **доступных резервных копий**.
4. Убедитесь, что для **состояния** отображается значение **Выполнено**.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о рабочем процессе по восстановлению после потери данных. См. дополнительные сведения по [восстановлению после масштабной потери данных](azure-stack-backup-recover-data.md).
