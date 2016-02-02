<properties
	pageTitle="Подготовка среды для резервного копирования Windows Server или клиента Windows | Microsoft Azure"
	description="Подготовка среды для резервного копирования: создание резервного хранилища, загрузка учетных данных и установка агента резервного копирования."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="хранилище архивации; агент резервного копирования; резервное копирование Windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Подготовка среды для резервного копирования виртуальных машин Azure
В этой статье описано, как подготовиться к резервному копированию Windows Server или клиента Windows в Azure. Для этого необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

Если это уже сделано, можно приступать к [резервному копированию виртуальных машин Windows](backup-azure-backup-windows-server.md). В противном случае выполните инструкции ниже, чтобы подготовить свою среду.

>[AZURE.NOTE] Ранее требовалось создать или получить сертификат X.509 v3 для регистрации сервера архивации. Сертификаты по-прежнему поддерживаются, но теперь, чтобы облегчить регистрацию хранилища Azure на сервере, можно создавать учетные данные хранилища прямо на странице "Быстрый запуск".

## Перед началом работы
Чтобы выполнить архивацию файлов и данных Windows Server в Azure, сначала необходимо выполнить следующие задачи.

- **Создание хранилища службы архивации**. [Создайте хранилище службы архивации на портале управления Azure](http://manage.windowsazure.com).
- **Загрузка учетных данных хранилища**. На странице панели мониторинга в хранилище службы архивации Azure загрузите учетные данные хранилища, которые будут использоваться для регистрации компьютера Windows в хранилище службы архивации.
- **Установка агента службы архивации Azure и регистрация сервера**. На странице панели мониторинга щелкните ссылку, чтобы загрузить [Агент службы архивации Azure](http://aka.ms/azurebackup_agent). Установите агент и зарегистрируйте сервер в хранилище службы архивации с помощью файла учетных данных хранилища.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Дальнейшие действия
- [Архивация Windows Server или клиента Windows](backup-azure-backup-windows-server.md)
- [Управление Windows Server или клиентом Windows](backup-azure-manage-windows-server.md)
- [Восстановление Windows Server или клиента Windows из Azure](backup-azure-restore-windows-server.md)
- [Часто задаваемые вопросы о службе архивации Azure](backup-azure-backup-faq.md)
- [Форум по службе архивации Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0128_2016-->