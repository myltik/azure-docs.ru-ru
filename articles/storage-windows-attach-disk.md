<properties linkid="manage-linux-howto-attach-a-disk" urlDisplayName="Attach a disk" pageTitle="Attach a disk to a virtual machine | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="Learn how to attach a data disk to an Azure virtual machine and initialize it so it's ready for use." metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="How to Attach a Data Disk to a Virtual Machine" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Как присоединить диск данных к виртуальной машине

Можно присоединять пустые диски и диски с данными. В обоих случаях это фактически файлы .vhd, которые размещаются в вашей учетной записи хранения Azure. Также в обоих случаях после присоединения диска необходимо его инициализировать, чтобы подготовить к использованию.

> [WACOM.NOTE] Рекомендуется использовать один или несколько отдельных дисков для хранения данных виртуальной машины. При создании виртуальной машины Azure у нее есть диск для операционной системы (диск C) и временный диск (диск D). **Не используйте диск D для хранения данных.** Как и предполагает его имя, он предназначен только для временного хранения. Этот диск не обеспечивает избыточности или резервного копирования, поскольку он не размещается в хранилище Azure.

-   [Практическое руководство. Присоединение пустого диска][]
-   [Практическое руководство. Присоединение существующего диска][]
-   [Практическое руководство. Инициализация нового диска данных в Windows Server][]

[WACOM.INCLUDE [руководство-присоединение-диск-windows-linux][]]

## <span id="initializeinWS"></span></a>Практическое руководство. Инициализация нового диска данных в Windows Server

1.  Подключитесь к виртуальной машине, выполнив действия, описанные в разделе [Как войти в систему на виртуальной машине, работающей под управлением Windows Server][].

2.  После входа в систему откройте **Диспетчер сервера**, на левой панели разверните вкладку **Хранилище** и нажмите кнопку **Управление дисками**.

    ![Откройте диспетчер сервера.][]

3.  Щелкните правой кнопкой мыши **Disk 2**, щелкните **Инициализировать диск**, а затем нажмите **OK**.

    ![Инициализировать диск][]

4.  Щелкните правой кнопкой мыши область выделения места на диске 2, нажмите кнопку **Создать простой том**, а затем завершите работу мастера со значениями по умолчанию.

    ![Инициализация тома][]

    The disk is now online and ready to use with a new drive letter.



    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [Практическое руководство. Присоединение пустого диска]: #attachempty
  [Практическое руководство. Присоединение существующего диска]: #attachexisting
  [Практическое руководство. Инициализация нового диска данных в Windows Server]: #initializeinWS
  [руководство-присоединение-диск-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Как войти в систему на виртуальной машине, работающей под управлением Windows Server]: ../virtual-machines-log-on-windows-server/
  [Откройте диспетчер сервера.]: ./media/storage-windows-attach-disk/ServerManager.png
  [Инициализировать диск]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [Инициализация тома]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
