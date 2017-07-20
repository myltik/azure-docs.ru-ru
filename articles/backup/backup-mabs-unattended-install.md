---
title: "Автоматическая установка Azure Backup Server версии 2 | Документация Майкрософт"
description: "Использование скрипта PowerShell для автоматической установки Azure Backup Server версии 2. Этот тип установки также называется \"тихой\" установкой."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Запуск автоматической установки Azure Backup Server версии 2

Узнайте, как запустить автоматическую установку Azure Backup Server версии 2. 

Эти действия не применяются, если выполняется установка Azure Backup Server версии 1.

## <a name="install-backup-server-v2"></a>Установка Backup Server версии 2

1. Создайте текстовый файл на сервере, на котором размещен Azure Backup Server версии 2. (Файл можно создать в блокноте или в другом текстовом редакторе.) Сохраните файл как MABSSetup.ini. 

2. Вставьте следующий код в файл MABSSetup.ini. Замените текст в скобках (\< \>) значениями из среды. Ниже приведен пример текста.

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. Сохраните файл. Затем в командной строке с повышенными привилегиями на сервере установки введите следующую команду:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

Для установки можно использовать следующие флаги:</br>
**/f** — путь к файлу.</br>
**/l** — путь к журналу.</br>
**/i** — путь для установки.</br>
**/x** — путь для удаления.</br>

## <a name="next-steps"></a>Дальнейшие действия
После установки Backup Server узнайте, как подготовить сервер или обеспечить защиту рабочей нагрузки.

- [Подготовка к резервному копированию рабочих нагрузок с использованием Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Резервное копирование сервера VMware в Azure](backup-azure-backup-server-vmware.md)
- [Архивация баз данных SQL Server в Azure с помощью Azure Backup Server](backup-azure-sql-mabs.md)
- [Добавление хранилища на Azure Backup Server версии 2](backup-mabs-add-storage.md)

