<properties
   pageTitle="Резервное копирование файлов и папок Windows Server или Windows Client в Azure | Microsoft Azure"
   description="Архивация данных Windows Server или Windows Client в Azure — это просто. Можно архивировать файлы и папки Windows в облако, выполнив несколько простых действий."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="архивация Windows Server; архивировать Windows Server"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="01/22/2016"
	 ms.author="jimpark;"/>

# Резервное копирование файлов и папок Windows Server или Windows Client в Azure
С помощью этой простой процедуры легко архивировать файлы и папки Windows в Azure. Прежде чем продолжить, выполните [необходимые требования](backup-configure-vault.md#before-you-start) (если вы еще этого не сделали), чтобы подготовить среду к архивации данных компьютера под управлением Windows.

## Резервное копирование файлов и папок
1. После регистрации компьютера откройте оснастку MMC в службе архивации Microsoft Azure.

    ![Результат поиска](./media/backup-azure-backup-windows-server/result.png)

2. Щелкните **Создать расписание архивации**.

    ![Планирование архивации Windows Server](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Выберите элементы, резервные копии которых надо создать. Служба архивации Azure на сервере или клиенте Windows (т. е. без System Center Data Protection Manager) позволяет защитить файлы и папки.

    ![Элементы для архивации Windows Server](./media/backup-azure-backup-windows-server/items.png)

4. Укажите расписание архивации и политику хранения, как описано в [этой статье](backup-azure-backup-cloud-as-tape.md).

5. Выберите метод отправки начальной резервной копии. Выбор параметров для начального заполнения зависит от объема данных, для которых необходимо выполнить резервное копирование, и от скорости вашего интернет-соединения. Если вы планируете создать резервную копию нескольких гигабайт или терабайт данных через сеть с высокой задержкой и низкой пропускной способностью, рекомендуется выполнить начальное резервное копирование, отправив диск в ближайший центр обработки данных Azure. Это называется автономным резервным копированием, о чем подробно описано в [этой статье](backup-azure-backup-import-export.md). При наличии сети с достаточной пропускной способностью рекомендуется выполнять начальное резервное копирование по сети.

    ![Начальная архивация Windows Server](./media/backup-azure-backup-windows-server/initialbackup.png)

6. После завершения процесса планирования архивации вернитесь к оснастке консоли MMC и выберите команду **Выполнить моментальную архивацию**, чтобы завершить начальное заполнение по сети.

    ![Моментальная архивация Windows Server](./media/backup-azure-backup-windows-server/backupnow.png)

7. После завершения начального заполнения в консоли службы архивации Azure в представлении **Задания** отобразится соответствующее состояние.

    ![Первоначальное восстановление завершено](./media/backup-azure-backup-windows-server/ircomplete.png)

## Дальнейшие действия
- [Управление Windows Server или клиентом Windows](backup-azure-manage-windows-server.md)
- [Восстановление Windows Server или клиента Windows из Azure](backup-azure-restore-windows-server.md)
- [Часто задаваемые вопросы о службе архивации Azure](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0128_2016-->