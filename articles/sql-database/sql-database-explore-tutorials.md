---
title: "Обзор руководств по базе данных SQL Azure | Документация Майкрософт"
description: "Дополнительные сведения о функциях и возможностях базы данных SQL"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2f24c1c43300a3c5035fbb4e90c3d13f73c1bc74
ms.openlocfilehash: 9d9243d0385b24e8794dc2ac69978b41cdca8331


---
# <a name="explore-azure-sql-database-tutorials"></a>Изучение учебников по базе данных SQL Azure
Приведенные ниже ссылки позволяют ознакомиться с обзором каждой из перечисленных функциональных областей и соответствующему простому пошаговому учебнику. Краткие руководства по конкретным решениям, демонстрирующие использование базы данных SQL в полноценном решении на основе реальных сценариев, см. в статье [Изучение кратких руководств по решениям базы данных SQL Azure](sql-database-solution-quick-starts.md).

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>Создание серверов, баз данных и правил брандмауэра на уровне сервера
В следующих руководствах описывается создание серверов, баз данных и правил брандмауэра на уровне сервера, а также содержатся сведения о подключении и отправке запросов к серверам и базам данных.

| Учебник | Описание |
| --- | --- | 
| [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md) | В этом руководстве объясняется, как с помощью портала Azure и SQL Server Management Studio создать логический сервер базы данных SQL Azure, просмотреть его свойства, создать брандмауэр на уровне сервера, подключиться к базе данных master, создать образец базы данных, просмотреть его свойства, подключиться к образцу базы данных и создать пустую базу данных. |
| [Создание базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell](sql-database-get-started-powershell.md) | В этом руководстве объясняется, как с помощью PowerShell создать логический сервер базы данных SQL Azure, просмотреть его свойства, создать брандмауэр на уровне сервера, подключиться к базе данных master, создать образец базы данных, просмотреть его свойства, подключиться к образцу базы данных и создать пустую базу данных. |
| [Руководство по базам данных SQL. Создание базы данных SQL с помощью C# и библиотеки базы данных SQL для .NET](sql-database-get-started-csharp.md)| В этом руководстве описано создание сервера базы данных SQL, правила брандмауэра и базы данных SQL на языке C#. Также можно создать приложение Active Directory (AD) и субъект-службу, необходимые для проверки подлинности в приложении C#. |
|  | |

## <a name="backup-and-recovery"></a>Архивация и восстановление
В следующем руководстве описывается восстановление базы данных на момент времени, настройка долгосрочного хранения резервных копий и восстановление базы данных из хранилища служб восстановления Azure. 

| Учебник | Описание |
| --- | --- | 
| [Get Started with Backup and Restore for Data Protection and Recovery](sql-database-get-started-backup-recovery.md) (Приступая к работе с резервным копированием и восстановлением для защиты и восстановления данных)| В этом руководстве описывается восстановление базы данных на момент времени, настройка долгосрочного хранения резервных копий и восстановление базы данных из хранилища служб восстановления Azure с помощью портала Azure. |
|  | |

## <a name="elastic-pools"></a>Пулы эластичных БД
В приведенных ниже учебниках вы можете узнать, как использовать [пулы эластичных БД](sql-database-elastic-pool.md), чтобы обеспечить простое и экономически выгодное решение для управления целевыми показателями производительности для нескольких баз данных с совершенно разными и непредсказуемыми моделями использования.

| Учебник | Описание |
| --- | --- | 
| [Создание эластичного пула](sql-database-elastic-pool-create-portal.md) |В этом руководстве вы можете узнать, как создать масштабируемый пул баз данных Azure SQL. |
| [Мониторинг эластичной базы данных](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) |В этом руководстве вы можете узнать, как отслеживать отдельную эластичную базу данных на наличие потенциальных проблем. |
| [Добавление оповещения в пул ресурсов](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) |В этом руководстве вы можете узнать, как можно добавить правила для отправки сообщений электронной почты конкретным людям в ресурсы или строки оповещений в конечные точки URL-адресов, когда ресурс достигает заданного порогового значения использования. |
| [Перемещение базы данных в пул эластичных БД](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) |В этом руководстве вы можете узнать, как переместить базу данных в пул эластичных БД. |
| [Перемещение базы данных из пула эластичных БД](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) |В этом руководстве вы можете узнать, как переместить базу данных из пула эластичных БД. |
| [Изменение параметров производительности пула](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) |В этом руководстве вы можете узнать, как настроить пределы производительности и хранения для пула. |
|  | |

## <a name="elastic-database-jobs"></a>Задания обработки эластичных баз данных
В следующих учебниках вы узнаете об использовании [заданий обработки эластичных баз данных](sql-database-elastic-jobs-overview.md).

| Учебник | Описание |
| --- | --- | 
| [Приступая к работе с инструментами эластичных баз данных](sql-database-elastic-scale-get-started.md) |В этом руководстве вы можете узнать, как использовать возможности инструментов эластичной базы данных с помощью простого сегментированного приложения. |
| [Начало работы с эластичными заданиями Базы данных SQL Azure](sql-database-elastic-jobs-getting-started.md) |В этом руководстве вы узнаете, как создавать и настраивать задания, которые управляют группой связанных баз данных. |
|  | |

## <a name="elastic-queries"></a>Эластичные запросы
В следующих учебниках вы узнаете об использовании [запросов к эластичным базам данных](sql-database-elastic-query-overview.md).

| Учебник | Описание |
| --- | --- | 
| [Запросы к горизонтально секционированной (сегментированной) базе данных](sql-database-elastic-query-getting-started.md) |В этом учебнике вы узнаете, как создавать отчеты из всех баз данных в горизонтально секционированной (сегментированной) базе данных с помощью [запроса к эластичной базе данных](sql-database-elastic-query-overview.md) |
| [Запросы к вертикально секционированной базе данных](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) |В этом учебнике вы узнаете, как создавать отчеты из всех баз данных в вертикально секционированной базе данных с помощью [запроса к эластичной базе данных](sql-database-elastic-query-overview.md) |
| [Перенос существующих баз данных для масштабирования](sql-database-elastic-convert-to-use-elastic-tools.md) |В этом руководстве вы можете узнать, как горизонтально масштабировать (сегментировать) Базу данных SQL Azure. |
|  | |

## <a name="performance-optimization"></a>Оптимизация производительности
В следующих руководствах содержатся сведения об оптимизации [производительности автономных баз данных](sql-database-performance-guidance.md). Для оптимизации производительности нескольких баз данных см. раздел [Пулы эластичных БД](#elastic-pools).

| Учебник | Описание |
| --- | --- | 
| [Изменение уровня обслуживания и уровня производительности базы данных](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) |В этом руководстве вы можете узнать, как масштабировать производительность базы данных SQL Azure вверх или вниз с помощью уровней обслуживания. |
| [Анализ производительности запросов в помощнике по работе с базами данных SQL](sql-database-performance.md#performance-overview) |В этом руководстве вы можете узнать, как открыть и использовать анализ производительности запросов в помощнике по работе с Базами данных SQL. |
| [Рекомендации по производительности для помощника по работе с Базами данных SQL](sql-database-advisor.md) |В этом руководстве вы можете узнать, как просмотреть и применить рекомендации по производительности для помощника по работе с Базами данных SQL. |
| [Просмотр запросов, максимально использующих ресурсы процессора](sql-database-query-performance.md#review-top-cpu-consuming-queries) |В этом руководстве вы можете узнать, как использовать анализ производительности запросов в помощнике по работе с Базами данных SQL для просмотра запросов, наиболее активно использующих ресурсы процессора. |
| [Просмотр подробных сведений для отдельного запроса](sql-database-query-performance.md#viewing-individual-query-details) |В этом руководстве вы можете узнать, как использовать анализ производительности запросов в помощнике по работе с Базами данных SQL для просмотра сведений о производительности отдельного запроса. |
|  | |

## <a name="sql-database-migration-and-archive"></a>Миграция и архивация баз данных SQL
В следующих учебниках вы узнаете о [переносе существующей базы данных SQL Server в базу данных SQL Azure](sql-database-cloud-migrate.md).

| Учебник | Описание |
| --- | --- | 
| [Обнаружение проблем совместимости с помощью SQL Server Data Tools для Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |В этом руководстве вы можете узнать, как использовать SQL Server Data Tools для Visual Studio, чтобы определить совместимость базы данных SQL Azure. |
| [Устранение проблем совместимости с помощью SQL Server Data Tools для Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |В этом руководстве вы можете узнать, как использовать SQL Server Data Tools для Visual Studio, чтобы исправить проблемы с совместимостью базы данных SQL Azure. |
| [Определение совместимости Базы данных SQL с помощью SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) |В этом руководстве вы можете узнать, как использовать программу командной строки SQLPackage.exe, чтобы определить совместимость базы данных SQL Azure. |
| [Определение совместимости Базы данных SQL с помощью SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |В этом руководстве вы можете узнать, как использовать SQL Server Management Studio, чтобы определить совместимость базы данных SQL Azure. |
| [Миграция базы данных SQL Server в Базу данных SQL с помощью мастера развертывания базы данных в Базу данных Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) |В этом руководстве вы можете узнать, как перенести совместимую базу данных SQL Server в базу данных SQL Azure с помощью мастера развертывания базы данных в базу данных Microsoft Azure в среде SQL Server Management Studio. |
| [Экспорт базы данных SQL Server в BACPAC-файл с помощью SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) |В этом руководстве вы можете узнать, как экспортировать совместимую базу данных SQL Server в BACPAC-файл с помощью мастера экспорта приложений уровня данных в среде SQL Server Management Studio. |
| [Экспорт базы данных SQL Server в BACPAC-файл с помощью SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) |В этом руководстве вы можете узнать, как экспортировать совместимую базу данных SQL Server в BACPAC-файл с помощью программы командной строки SQLPackage.exe. |
| [Импорт BACPAC-файла в Базу данных SQL Azure с помощью SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) |В этом руководстве вы можете узнать, как импортировать базу данных SQL Azure из BACPAC-файла с помощью мастера экспорта приложений уровня данных в среде SQL Server Management Studio. |
| [Импорт BACPAC-файла в Базу данных SQL Azure с помощью SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) |В этом руководстве вы можете узнать, как импортировать базу данных SQL Azure из BACPAC-файла с помощью программы командной строки SQLPackage. |
| [Импорт BACPAC-файла в Базу данных SQL Azure с помощью портала Azure](sql-database-import.md) |В этом руководстве вы можете узнать, как импортировать базу данных SQL Azure из BACPAC-файла, хранящегося в BLOB-объекте Azure, с помощью портала Azure. |
| [Импорт BACPAC-файла в Базу данных SQL Azure с помощью PowerShell](sql-database-import-powershell.md) |В этом руководстве вы можете узнать, как импортировать базу данных SQL Azure из BACPAC-файла с помощью PowerShell. |
| [Архивация Базы данных SQL Azure с помощью портала Azure](sql-database-export.md#export-your-database) |В этом руководстве вы можете узнать, как заархивировать базу данных SQL Azure в BACPAC-файл с помощью портала Azure. |
| [Архивация Базы данных SQL Azure с помощью PowerShell](sql-database-export-powershell.md) |В этом учебнике вы узнаете, как заархивировать базу данных SQL Azure в BACPAC-файл с помощью PowerShell. |
| [Копирование Базы данных SQL Azure с помощью портала Azure](sql-database-copy.md) |В этом руководстве вы можете узнать, как скопировать базу данных SQL Azure с помощью портала Azure. |
| [Копирование базы данных SQL Azure с помощью PowerShell](sql-database-copy-powershell.md) |В этом руководстве вы можете узнать, как скопировать базу данных SQL Azure с помощью PowerShell. |
| [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) |В этом руководстве вы можете узнать, как скопировать базу данных SQL Azure с помощью Transact-SQL. |
|  | |

## <a name="develop"></a>Разработка
В следующих учебниках вы узнаете о [разработке базы данных SQL](sql-database-develop-overview.md) и использовании [библиотек подключений](sql-database-libraries.md).

| Учебник | Описание |
| --- | --- | 
| [Подключение к базе данных SQL с помощью .NET (C#)](sql-database-develop-dotnet-simple.md) |В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью C#. |
| [Подключение к Базе данных SQL с помощью Java](sql-database-develop-java-simple.md) |В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью Java. |
| [Подключение к базе данных SQL с помощью Node.js](sql-database-develop-nodejs-simple.md) |В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью Node.js. |
| [Подключение к Базе данных SQL с помощью PHP](sql-database-develop-php-simple.md) |В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью PHP. |
| [Подключение к базе данных SQL с помощью Python](sql-database-develop-python-simple.md) |В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью Python. |
| [Подключение к базе данных SQL с помощью Ruby](sql-database-develop-ruby-simple.md) |В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью Ruby. |
|  | |

## <a name="database-authentication-and-authorization"></a>Проверка подлинности и авторизация в базе данных
В следующих учебниках вы узнаете, как [создавать имена для входа и пользователей и управлять ими](sql-database-manage-logins.md).

| Учебник | Описание |
| --- | --- | 
| [Использование аутентификации SQL Server) |Из этого учебника вы узнаете, как предоставлять доступ к серверам и базам данных SQL Azure, а также и настраивать для них разрешения, используя в SQL Server Management Studio аутентификацию SQL Server, имена для входа, пользователей и роли баз данных. |
|  | |

## <a name="secure-and-protect-data"></a>Защита и обеспечение безопасности данных
В следующих учебниках вы узнаете о [защите данных в базе данных SQL Azure](sql-database-security-overview.md).

| Учебник | Описание |
| --- | --- | 
| [Включение обнаружения угроз для базы данных с помощью портала Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) |В этом руководстве вы можете узнать, как настроить обнаружение угроз для базы данных на портале Azure. |
| [Защита конфиденциальных данных с помощью постоянного шифрования ](sql-database-always-encrypted-azure-key-vault.md) |В этом руководстве вы можете узнать, как использовать мастер постоянного шифрования для защиты конфиденциальных данных в Базе данных SQL Azure. |
| [Защита конфиденциальных данных с помощью прозрачного шифрования](https://msdn.microsoft.com/library/dn948096.aspx) |В этом руководстве вы можете узнать, как использовать прозрачное шифрование для защиты конфиденциальных данных. |
| [Шифрование столбца данных](https://msdn.microsoft.com/library/ms179331.aspx) |В этом руководстве вы можете узнать, как зашифровать столбец данных с помощью Transact-SQL. |
| [Настройка динамического маскирования данных](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) |В этом руководстве вы можете узнать, как настроить динамическое маскирование данных для базы данных SQL Azure. |
|  | |

## <a name="business-continuity"></a>Непрерывность бизнес-процессов
В следующих учебниках вы узнаете об использовании [геовосстановления и активной георепликации](sql-database-business-continuity.md) для восстановления после ошибок, обеспечения непрерывности бизнес-процессов и развертывания запросов.

| Учебник | Описание |
| --- | --- | 
| [Восстановление базы данных SQL Azure до момента времени в прошлом с помощью портала Azure](sql-database-point-in-time-restore.md) |В этом руководстве вы можете узнать, как восстановить базу данных до момента времени в прошлом с помощью портала Azure. |
| [Восстановление базы данных SQL Azure до точки во времени с помощью PowerShell](sql-database-point-in-time-restore.md) |В этом руководстве вы можете узнать, как восстановить базу данных до момента времени в прошлом с помощью PowerShell. |
| [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-portal.md) |В этом руководстве вы можете узнать, как восстановить удаленную базу данных с помощью портала Azure. |
| [Восстановление удаленной Базы данных SQL Azure с помощью PowerShell](sql-database-restore-deleted-database-powershell.md) |В этом руководстве вы можете узнать, как восстановить удаленную базу данных с помощью PowerShell. |
| [Настройка георепликации для базы данных SQL Azure с помощью портала Azure](sql-database-geo-replication-portal.md) |В этом учебнике вы узнаете, как настроить активную георепликацию с помощью портала Azure. |
| [Настройка георепликации для базы данных SQL Azure с помощью PowerShell](sql-database-geo-replication-powershell.md) |В этом учебнике вы узнаете, как настроить активную георепликацию с помощью PowerShell. |
| [Настройка георепликации базы данных SQL Azure с помощью Transact-SQL](sql-database-geo-replication-transact-sql.md) |В этом учебнике вы узнаете, как настроить активную георепликацию с помощью Transact-SQL. |
| [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure](sql-database-geo-replication-failover-portal.md) |В этом руководстве вы можете узнать, как выполнить отработку отказа на геореплицированную вторичную реплику с помощью портала Azure. |
| [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью PowerShell](sql-database-geo-replication-failover-powershell.md) |В этом руководстве вы можете узнать, как выполнить отработку отказа на геореплицированную вторичную реплику с помощью PowerShell. |
| [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) |В этом руководстве вы можете узнать, как выполнить отработку отказа на геореплицированную вторичную реплику с помощью Transact-SQL. |
|  | |

## <a name="data-sync"></a>Синхронизация данных
В этом учебнике вы узнаете о [синхронизации данных](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Учебник | Описание |
| --- | --- | 
| [Приступая к работе с синхронизацией данных SQL Azure (предварительная версия)](sql-database-get-started-sql-data-sync.md) |В этом учебнике вы ознакомитесь с основами синхронизации данных SQL Azure с использованием классического портала Azure. |
|  | |

## <a name="next-steps"></a>Дальнейшие действия
[Изучение кратких руководств по решениям базы данных SQL Azure](sql-database-solution-quick-starts.md)



<!--HONumber=Jan17_HO3-->


