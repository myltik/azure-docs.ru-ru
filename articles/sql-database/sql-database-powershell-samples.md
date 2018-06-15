---
title: Примеры скриптов Azure PowerShell для базы данных SQL | Документация Майкрософт
description: Примеры скриптов Azure PowerShell помогут вам создать серверы базы данных SQL, эластичные пулы, базы данных и брандмауэры, а также помогут управлять ими.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: tysonn
tags: azure-service-management
ms.service: sql-database
ms.custom: overview-samples, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 2245964cab3361e0f298cf35d26e8b0a1b8456f0
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30924125"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Примеры Azure PowerShell для базы данных SQL Azure

В следующей таблице содержатся ссылки на примеры сценариев Azure PowerShell для базы данных SQL Azure.

| |  |
|---|---|
|**Создание отдельной базы данных и эластичного пула**||
| [Создание отдельной базы данных и настройка правила брандмауэра](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell создает отдельную базу данных SQL Azure и настраивает правило брандмауэра на уровне сервера. |
| [Создание эластичных пулов и перемещение баз данных в составе пулов](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell создает эластичные пулы базы данных SQL Azure и перемещает базы данных в составе пулов, а также изменяет уровни производительности.|
|**Настройка георепликации и отработка отказа**||
| [Настройка активной георепликации для отдельной базы данных SQL Azure с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell настраивает активную георепликацию для отдельной базы данных SQL Azure и выполняет для нее отработку отказа во вторичную реплику. |
| [Настройка активной георепликации для базы данных SQL Azure в составе пула с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell настраивает активную георепликацию для базы данных SQL Azure в эластичном пуле SQL и выполняет для нее отработку отказа во вторичную реплику. |
| [Use PowerShell to configure an active geo-replication failover group for a single Azure SQL database](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Использование PowerShell для настройки группы отработки отказа активной георепликации для отдельной базы данных SQL Azure (предварительная версия)) | Этот скрипт PowerShell настраивает группу отработки отказа для экземпляра сервера базы данных SQL Azure, добавляет базу данных в группу отработки отказа и выполняет для нее отработку отказа на сервер-получатель. |
|**Масштабирование отдельных баз данных и эластичного пула**||
| [Масштабирование отдельной базы данных](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell отслеживает метрики производительности базы данных SQL Azure, масштабирует ее до более высокого уровня производительности и создает правило генерации оповещений для одной из метрик производительности. |
| [Масштабирование эластичного пула](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell отслеживает метрики производительности эластичного пула базы данных SQL Azure, масштабирует его до более высокого уровня производительности и создает правило генерации оповещений для одной из метрик производительности.  |
| **Аудит и обнаружение угроз** |
| [Настройка аудита и обнаружения угроз](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell настраивает политики аудита и обнаружения угроз для базы данных SQL Azure. |
| **Восстановление, копирование и импорт базы данных**||
| [Восстановление базы данных](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell восстанавливает базу данных SQL Azure из геоизбыточной резервной копии и восстанавливает последнюю резервную копию удаленной базы данных SQL Azure. |
| [Копирование базы данных на новый сервер](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell создает копию существующей базы данных SQL Azure на новом сервере SQL Azure. |
| [Импорт базы данных из BACPAC-файла](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell импортирует базу данных из BACPAC-файла на сервер SQL Azure. |
| **Синхронизация данных между базами данных**||
| [Синхронизация данных между базами данных SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell настраивает синхронизацию данных между несколькими базами данных SQL Azure. |
| [Синхронизация данных между базой данных SQL и локальной базой данных SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell настраивает синхронизацию данных между базой данных SQL Azure и локальной базой данных SQL Server. |
| [Изменение схемы синхронизации данных SQL](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell добавляет или удаляет элементы в схеме синхронизации данных. |
|||
