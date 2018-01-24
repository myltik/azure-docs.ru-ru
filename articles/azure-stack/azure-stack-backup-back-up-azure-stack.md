---
title: "Резервное копирование для Azure Stack | Документация Майкрософт"
description: "Выполните резервное копирование по требованию для Azure Stack с помощью резервного копирования."
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
ms.openlocfilehash: df1f4c6fadd08b17a1a1eb8bbe41ab71ae4729fc
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="back-up-azure-stack"></a>Резервное копирование для Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Выполните резервное копирование по требованию для Azure Stack с помощью резервного копирования. Если необходимо включить службу резервного копирования инфраструктуры, см. инструкции по [включению резервного копирования для Azure Stack на портале администрирования](azure-stack-backup-enable-backup-console.md).

## <a name="start-azure-stack-backup"></a>Запуск резервного копирования Azure Stack

Откройте Windows PowerShell из командной строки с повышенными привилегиями и выполните следующие команды:

   ```powershell
   Start-AzSBackup -Location $location
   ```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Подтверждение завершения резервного копирования на портале администрирования

1. Войдите на портал администрирования Azure Stack по адресу [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Выберите **Дополнительные службы** > **Резервное копирование инфраструктуры**. Выберите **Конфигурация** в колонке **Резервное копирование инфраструктуры**.
3. Найти **имя** и **дату завершения** резервного копирования в списке **доступных резервных копий**.
4. Убедитесь, что для **состояния** отображается значение **Выполнено**.

Вы можете также подтвердить завершение резервного копирования на портале администрирования. Перейдите на страницу `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`.

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о рабочем процессе по восстановлению после потери данных. См. дополнительные сведения по [восстановлению после масштабной потери данных](azure-stack-backup-recover-data.md).