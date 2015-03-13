<properties 
	pageTitle="Присоединение диска данных к виртуальной машине Azure" 
	description="Узнайте, как присоединить диск данных к виртуальной машине Azure и инициализировать, чтобы подготовить к использованию." 
	services="virtual-machines, storage" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2015" 
	ms.author="kathydav"/>

#Как присоединить диск данных к виртуальной машине

Можно присоединять пустые диски и диски с данными. В обоих случаях это фактически файлы .vhd, которые размещаются в вашей учетной записи хранения Azure. Также в обоих случаях после присоединения диска необходимо его инициализировать, чтобы подготовить к использованию. 

> [AZURE.NOTE] Рекомендуется использовать один или несколько отдельных дисков для хранения данных виртуальной машины. При создании виртуальной машины Azure у нее есть диск для операционной системы (диск C) и временный диск (диск D). **Не используйте диск D для хранения данных.** Как и предполагает его имя, он предназначен только для временного хранения. Этот диск не обеспечивает избыточности или резервного копирования, поскольку он не размещается в хранилище Azure.

- [Практическое руководство. Присоединение пустого диска](#attachempty)
- [Практическое руководство. Присоединение существующего диска](#attachexisting)
- [Практическое руководство. Инициализация нового диска данных в Windows Server](#initializeinWS)


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinWS"></a>Практическое руководство. Инициализация нового диска данных в Windows Server

1. Подключитесь к виртуальной машине. Указания см. в разделе [Как войти в виртуальную машину под управлением Windows Server][logon].

2. После входа в систему откройте **Диспетчер сервера**, на левой панели разверните вкладку **Хранилище** и нажмите кнопку **Управление дисками**.

	![Open Server Manager](./media/storage-windows-attach-disk/ServerManager.png)

3. Щелкните правой кнопкой мыши **Disk 2**, выберите **Инициализировать диск** и нажмите **OK**.

	![Initialize the disk](./media/storage-windows-attach-disk/InitializeDisk.png)

4. Щелкните правой кнопкой мыши область выделения места на диске 2, нажмите кнопку **Создать простой том**, а затем завершите работу мастера со значениями по умолчанию.

	![Initialize the volume](./media/storage-windows-attach-disk/InitializeDiskVolume.png)

[logon]: ../virtual-machines-log-on-windows-server/

	Диск подключен и готов для использования с новой буквой диска.

	![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

> [AZURE.NOTE] Максимальное количество дисков, которые можно подключить к виртуальной машине, зависит от ее размера. Например, к виртуальной машине типа Standard A2 можно подключить только 4 диска, типа Standard D14 - 32 диска и типа Standard G5 - 64 диска. Дополнительную информацию о том, сколько дисков можно подключить к виртуальной машины в соответствии с ее размером, можно найти [здесь](https://msdn.microsoft.com/ru-ru/library/azure/dn197896.aspx).

<!--HONumber=42-->
