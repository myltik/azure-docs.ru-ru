---
title: "Использование Modern Backup Storage с Azure Backup Server версии 2 | Документация Майкрософт"
description: "Сведения о новых возможностях Azure Backup Server версии 2. В этой статье описывается обновление установки Backup Server."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Добавление хранилища на Azure Backup Server версии 2

Azure Backup Server версии 2 поставляется с Modern Backup Storage System Center 2016 Data Protection Manager. Modern Backup Storage обеспечивает до 50 % экономии пространства хранения, архивацию, которая в три раза быстрее, и более эффективное хранилище. Кроме того, предлагается хранение с учетом рабочих нагрузок. 

> [!NOTE]
> Чтобы использовать Modern Backup Storage, необходимо запустить Backup Server версии 2 в Windows Server 2016. Если запустить Backup Server версии 2 на более ранней версии Windows Server, Azure Backup Server не сможет реализовать преимущества Modern Backup Storage. Вместо этого он будет обеспечивать защиту рабочих нагрузок, как в случае с Backup Server версии 1. Дополнительные сведения см. в матрице защиты версий [Backup Server](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server-v2"></a>Тома в Backup Server версии 2

Backup Server версии 2 поддерживает тома хранилища. При добавлении тома Backup Server форматирует его в систему Resilient File System (ReFS), требуемую для Modern Backup Storage. Чтобы добавить том и при необходимости раскрыть его позже, рекомендуется использовать следующий рабочий процесс:

1.  Настройте Backup Server версии 2 на виртуальной машине.
2.  Создайте том на виртуальном диске в пуле носителей:
    1.  Добавьте диск в пул носителей и создайте виртуальный диск с простой структурой.
    2.  Добавьте дополнительные диски и расширьте виртуальный диск.
    3.  Создайте тома на виртуальном диске.
3.  Добавьте эти тома на Backup Server.
4.  Настройте хранилище с учетом рабочих нагрузок.

## <a name="create-a-volume-for-modern-backup-storage"></a>Создание тома для Modern Backup Storage

Использование Backup Server версии 2 в качестве дискового накопителя поможет полностью контролировать хранилище. Томом может быть один диск. Тем не менее, если вы хотите расширить хранилище в будущем, создайте том из дисков с незадействованным дисковым пространством. Это поможет при необходимости увеличить размер тома для хранения резервных копий. В данном разделе приведены рекомендации по созданию тома с такой конфигурацией.

1. В диспетчере сервера выберите **Файловые службы и службы хранилища** > **Тома** > **Пулы носителей**. В разделе **ФИЗИЧЕСКИЕ ДИСКИ** выберите **Новый пул носителей**. 

    ![Создание учетной записи хранения](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. В раскрывающемся списке **Задачи** выберите **New Virtual Disk** (Создать виртуальный диск).

    ![Добавление виртуального диска](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Выберите пул носителей, а затем выберите **Добавление физического диска**.

    ![Добавление физического диска](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Выберите физический диск, а затем выберите **Расширение виртуального диска**.

    ![Расширение виртуального диска](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Выберите виртуальный диск, а затем выберите **Новый том**.

    ![Создание нового тома](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. В диалоговом окне **Выбор сервера или диска** выберите сервер и новый диск. Затем нажмите кнопку **Далее**.

    ![Выбор сервера и диска](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Добавление тома для хранилища дисков Backup Server

Чтобы добавить том на Backup Server в области **Управление**, повторно просканируйте хранилище, а затем выберите **Добавить**. Отобразится список всех томов, которые можно добавить в Backup Server Storage. После добавления в список выбранных томов доступных томов можно присвоить им понятные имена, чтобы упростить управление. Чтобы отформатировать эти тома в ReFS и использовать преимущества Modern Backup Storage, нажмите кнопку **ОК**.

![Добавление доступных томов](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Настройка хранилища с учетом рабочих нагрузок

Используя хранилище с учетом рабочих нагрузок, можно выбрать предпочтительные тома для хранения определенных типов рабочих нагрузок. Например, можно указать ресурсоемкие тома, которые поддерживают большое количество операций ввода-вывода в секунду (IOPS), для хранения только рабочих нагрузок, требующих частого создания резервных копий большого объема. Примером может быть SQL Server с журналами транзакций. Рабочие нагрузки, архивация которых выполняется реже, например виртуальные машины, можно архивировать на недорогие тома.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Хранилище с учетом рабочих нагрузок можно настроить с помощью командлета PowerShell Update-DPMDiskStorage, обновляющего свойства тома в пуле носителей на сервере Data Protection Manager.

Синтаксис:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
На следующем изображении показан командлет Update-DPMDiskStorage в окне PowerShell.

![Команда Update-DPMDiskStorage в окне PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Изменения, внесенные с помощью PowerShell, отражаются в консоли администрирования Backup Server.

![Диски и тома в консоли администрирования](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Дальнейшие действия
После установки Backup Server узнайте, как подготовить сервер или обеспечить защиту рабочей нагрузки.

- [Подготовка к резервному копированию рабочих нагрузок с использованием Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Резервное копирование сервера VMware в Azure](backup-azure-backup-server-vmware.md)
- [Архивация баз данных SQL Server в Azure с помощью Azure Backup Server](backup-azure-sql-mabs.md)

