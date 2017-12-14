---
title: "Восстановление файлов из Azure в Windows Server | Документация Майкрософт"
description: "В этом руководстве представлены подробные сведения о восстановлении элементов из Azure в Windows Server."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "архивация windows server; восстановление файлов windows server; архивация и аварийное восстановление"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: b5f77ec04ef6d267583a6dc6a4476f118a4d0f74
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Восстановление файлов из Azure в Windows Server

Azure Backup обеспечивает восстановление отдельных элементов из резервных копий Windows Server. Восстановление отдельных файлов полезно, если необходимо быстро восстановить случайно удаленные файлы. В этом руководстве описано использование агента службы восстановления Microsoft Azure (MARS) для восстановления элементов из резервных копий в Azure. Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Запуск восстановления отдельных элементов 
> * выбора точки восстановления 
> * Восстановление элементов из точки восстановления

В этом руководстве предполагается, что вы уже выполнили действия по [архивации Windows Server в Azure](backup-configure-vault.md) и у вас есть хотя бы одна резервная копия файлов Windows Server в Azure.

## <a name="initiate-recovery-of-individual-items"></a>Запуск восстановления отдельных элементов

Мастер создания интерфейса пользователя Microsoft Azure Backup устанавливается вместе с агентом служб восстановления Microsoft Azure (MARS). Мастер Microsoft Azure Backup работает с агентом служб восстановления Microsoft Azure (MARS) для получения данных резервной копии из точек восстановления, сохраненных в Azure. Мастер Microsoft Azure Backup позволяет определить файлы или папки, которые требуется восстановить на Windows Server. 

1. Откройте оснастку **Microsoft Azure Backup**. Его можно найти, выполнив поиск строки **Microsoft Azure Backup** на компьютере.

    ![Статус "В ожидании резервного копирования"](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. В окне мастера щелкните **Восстановить данные** на **панели действий** консоли агента, чтобы запустить мастер **восстановления данных**.

    ![Статус "В ожидании резервного копирования"](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. На странице **Приступая к работе** выберите **This server (server name)** (Этот сервер (имя сервера)) и нажмите кнопку **Далее**.

4. На странице **Выбор режима восстановления** выберите **Отдельные файлы и папки** и нажмите кнопку **Далее**, чтобы начать процесс выбора точки восстановления.
 
5. На странице **Выбор тома и даты** выберите том, содержащий файлы или папки, которые необходимо восстановить, и щелкните **Подключить**. Из раскрывающегося меню выберите дату и время, соответствующие точке восстановления. Даты, выделенные **полужирным** шрифтом, означают доступность по крайней мере одной точки восстановления в указанный день.

    ![Статус "В ожидании резервного копирования"](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Если щелкнуть **Подключить**, Azure Backup сделает точку восстановления доступной в качестве диска. Найдите и восстановите файлы с диска.

## <a name="restore-items-from-a-recovery-point"></a>Восстановление элементов из точки восстановления

1. После подключения тома для восстановления щелкните **Обзор**, чтобы открыть проводник Windows, и найдите файлы и папки, которые нужно восстановить. 

    ![Статус "В ожидании резервного копирования"](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Файлы можно открыть непосредственно с тома для восстановления и проверить их.

2. В проводнике Windows скопируйте файлы и/или папки, которые требуется восстановить, и вставьте их в любое расположение на сервере.

    ![Статус "В ожидании резервного копирования"](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Завершив восстановление файлов и/или папок на странице **Browse and Recovery Files** (Поиск и восстановление файлов) мастера **восстановления данных**, щелкните **Отключить**. 

    ![Статус "В ожидании резервного копирования"](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Щелкните **Да**, чтобы подтвердить отключение тома.

    После отключения моментального снимка на панели консоли агента **Задания** появится сообщение **Задание завершено**.

## <a name="next-steps"></a>Дальнейшие действия

На этом завершается руководство по архивации и восстановлению данных Windows Server в Azure. Дополнительные сведения о службе Azure Backup см. в образце PowerShell для архивации зашифрованных виртуальных машин.

> [!div class="nextstepaction"]
> [Back up an encrypted Azure virtual machine with PowerShell](./scripts/backup-powershell-sample-backup-encrypted-vm.md) (Архивация зашифрованных виртуальных машин Azure с помощью PowerShell)
