<properties
   pageTitle="Резервное копирование файлов и папок Windows Server или Windows Client в Azure | Microsoft Azure"
   description="Узнайте, как выполнять резервное копирование из Windows Server или Windows Client в Azure."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/21/2015" ms.author="jimpark"; "aashishr"/>

# Резервное копирование файлов и папок Windows Server или Windows Client в Azure
В этой статье описывается процедура резервного копирования файлов и папок Windows Server или Windows Client в Azure.

## Перед началом работы
Прежде чем продолжить, выполните все [предварительные требования](backup-configure-vault.md#before-you-start) по использованию службы архивации Microsoft Azure для защиты Windows Server и Windows Client. Предварительные требования включают такие задачи, как создание хранилища архивации, скачивание учетных данных хранилища, установка агента службы архивации Azure и регистрация компьютера в хранилище.

## Резервное копирование файлов
1. После регистрации компьютера откройте оснастку MMC в службе архивации Microsoft Azure.

    ![Результат поиска](./media/backup-azure-backup-windows-server/result.png)

2. Щелкните **Создать расписание архивации**.

    ![Создание расписания архивации](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Выберите элементы, резервные копии которых надо создать. Служба архивации Azure на сервере или клиенте Windows (т. е. без System Center Data Protection Manager) позволяет защитить файлы и папки.

    ![Элементы для резервного копирования](./media/backup-azure-backup-windows-server/items.png)

4. Укажите расписание резервного копирования и политику хранения, как описано в [этой статье](backup-azure-backup-cloud-as-tape.md).

5. Выберите метод отправки начальной резервной копии. Выбор параметров для начального заполнения зависит от объема данных, для которых необходимо выполнить резервное копирование, и от скорости вашего интернет-соединения. Если вы планируете создать резервную копию нескольких гигабайт или терабайт данных через сеть с высокой задержкой и низкой пропускной способностью, рекомендуется выполнить начальное резервное копирование, отправив диск в ближайший центр обработки данных Azure. Это называется автономным резервным копированием, о чем подробно описано в [этой статье](backup-azure-backup-import-export.md). При наличии сети с достаточной пропускной способностью рекомендуется выполнять начальное резервное копирование по сети.

    ![Начальное резервное копирование](./media/backup-azure-backup-windows-server/initialbackup.png)

6. После завершения процесса планирования резервного копирования вернитесь к оснастке консоли MMC и выберите команду **Выполнить моментальную архивацию**, чтобы завершить начальное заполнение по сети.

    ![Команда «Выполнить моментальную архивацию»](./media/backup-azure-backup-windows-server/backupnow.png)

7. После завершения начального заполнения в консоли службы архивации Azure в представлении **Задания** отобразится соответствующее состояние.

    ![Первоначальное восстановление завершено](./media/backup-azure-backup-windows-server/ircomplete.png)

## Дальнейшие действия
- [Управление Windows Server или клиентом Windows](backup-azure-manage-windows-server.md)
- [Восстановление Windows Server или клиента Windows из Azure](backup-azure-restore-windows-server.md)
- [Часто задаваемые вопросы о службе архивации Azure](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO4-->