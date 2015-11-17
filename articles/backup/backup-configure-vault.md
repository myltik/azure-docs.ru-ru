<properties
	pageTitle="Настройка служб архивации Azure для подготовки к архивации Windows Server | Microsoft Azure"
	description="С помощью данного учебника можно получить необходимые навыки по использованию службы архивации в облачных решениях Microsoft Azure для архивирования Windows Server в облачную среду."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="11/03/2015" ms.author="jimpark"; "aashishr"/>

# Подготовка среды для резервного копирования виртуальных машин Azure

В этой статье рассказывается о том, как включить службу архивации Azure. Чтобы создать резервную копию Windows Server или клиента Windows Client в Azure, вам потребуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
>[AZURE.NOTE]Ранее требовалось создать или получить сертификат X.509 v3 для регистрации сервера архивации. Сертификаты по-прежнему поддерживаются, но теперь, чтобы облегчить регистрацию хранилища Azure на сервере, можно создавать учетные данные хранилища прямо на странице "Быстрый запуск".

## Перед началом работы
Чтобы выполнить архивацию файлов и данных Windows Server в Azure, сначала необходимо выполнить следующие задачи.

- **Создание хранилища службы архивации**. Создайте хранилище в консоли службы архивации Azure.
- **Скачивание учетных данных хранилища**. В службе архивации Azure загрузите в хранилище созданные вами сертификаты управления.
- **Установите агент службы архивации Azure и зарегистрируйте сервер**. В службе архивации Azure установите агент и зарегистрируйте сервер в хранилище архивации.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Дальнейшие действия
- [Архивация Windows Server или клиента Windows](backup-azure-backup-windows-server.md)
- [Управление Windows Server или клиентом Windows](backup-azure-manage-windows-server.md)
- [Восстановление Windows Server или клиента Windows из Azure](backup-azure-restore-windows-server.md)
- [Часто задаваемые вопросы о службе архивации Azure](backup-azure-backup-faq.md)
- [Форум по службе архивации Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=Nov15_HO3-->