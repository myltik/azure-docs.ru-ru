<properties
   pageTitle="Знакомство с руководствами по Базе данных SQL"
   description="Вы можете узнать о функциях и возможностях базы данных SQL"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/04/2016"
   ms.author="carlrab"/>
   
# Знакомство с руководствами по Базе данных SQL

Приведенные ниже ссылки позволяют перейти к обзору каждой из перечисленных функциональных областей и соответствующему краткому руководству.

## Использование среды SQL Server Management Studio

Приведенные ниже руководства описывают использование SQL Server Management Studio для администрирования Базы данных SQL Azure и выполнения запросов к ней.

| Учебник | Описание |
|---|---|---|
| [Подключение к базе данных SQL Azure с использованием имени субъекта на уровне сервера](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью имени субъекта на серверном уровне.|
| [Подключение пользователя к базе данных SQL Azure](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью имени пользователя на уровне базы данных.|
||||

## Пулы эластичных БД

В приведенных ниже руководствах вы можете узнать, как использовать [пулы эластичных БД](sql-database-elastic-pool.md), чтобы обеспечить простое и экономически выгодное решение для управления целевыми показателями производительности для нескольких баз данных с совершенно разными и непредсказуемыми моделями функционирования.

| Учебник | Описание |
|---|---|---|
| [Создание эластичного пула](sql-database-elastic-pool-create-portal.md) | В этом руководстве вы можете узнать, как создать масштабируемый пул баз данных Azure SQL. |
| [Мониторинг эластичной базы данных](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | В этом руководстве вы можете узнать, как отслеживать отдельную эластичную базу данных на наличие потенциальных проблем. |
| [Добавление оповещения в пул ресурсов](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | В этом руководстве вы можете узнать, как можно добавить правила для отправки сообщений электронной почты конкретным людям в ресурсы или строки оповещений в конечные точки URL-адресов, когда ресурс достигает заданного порогового значения использования. |
| [Перемещение базы данных в пул эластичных БД](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | В этом руководстве вы можете узнать, как переместить базу данных в пул эластичных БД. |
| [Перемещение базы данных из пула эластичных БД](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | В этом руководстве вы можете узнать, как переместить базу данных из пула эластичных БД. |
| [Изменение параметров производительности пула](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | В этом руководстве вы можете узнать, как настроить пределы производительности и хранения для пула. |
||||

## Задания обработки эластичных баз данных

В следующих руководствах вы можете узнать об использовании [заданий эластичной базы данных](sql-database-elastic-jobs-overview.md).

| Учебник | Описание |
|---|---|---|
| [Приступая к работе с инструментами эластичных баз данных](sql-database-elastic-scale-get-started.md) | В этом руководстве вы можете узнать, как использовать возможности инструментов эластичной базы данных с помощью простого сегментированного приложения. |
| [Начало работы с эластичными заданиями Базы данных SQL Azure](sql-database-elastic-jobs-getting-started.md) | В этом руководстве вы можете узнать, как создавать и настраивать задания, которые управляют группой связанных баз данных. | 
||||

## Эластичные запросы

В следующих руководствах вы можете узнать об использовании [эластичных запросов](sql-database-elastic-query-overview.md).

| Учебник | Описание |
|---|---|---|
| [Запросы к горизонтально секционированной (сегментированной) базе данных](sql-database-elastic-query-getting-started.md) | В этом руководстве вы можете узнать, как создавать отчеты из всех баз данных в горизонтально секционированной (сегментированной) базе данных с помощью [эластичного запроса](sql-database-elastic-query-overview.md). |
| [Запросы к вертикально секционированной базе данных](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | В этом руководстве вы можете узнать, как создавать отчеты из всех баз данных в вертикально секционированной базе данных с помощью [эластичного запроса](sql-database-elastic-query-overview.md). |
| [Перенос существующих баз данных для масштабирования](sql-database-elastic-convert-to-use-elastic-tools.md)| В этом руководстве вы можете узнать, как горизонтально масштабировать (сегментировать) Базу данных SQL Azure. |
||||

## Оптимизация производительности

В следующих руководствах вы можете узнать об оптимизации [производительности отдельных баз данных](sql-database-performance-guidance.md). Для оптимизации производительности нескольких баз данных см. руководство [Пулы эластичных БД](#elastic-pools).

| Учебник | Описание |
|---|---|---|
| [Изменение уровня обслуживания и уровня производительности базы данных](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | В этом руководстве вы можете узнать, как масштабировать производительность базы данных SQL Azure вверх или вниз с помощью уровней обслуживания. |
| [Анализ производительности запросов в помощнике по работе с базами данных SQL](sql-database-performance.md#performance-overview) | В этом руководстве вы можете узнать, как открыть и использовать анализ производительности запросов в помощнике по работе с Базами данных SQL.|
| [Рекомендации по производительности для помощника по работе с Базами данных SQL](sql-database-index-advisor.md#viewing-recommendations) | В этом руководстве вы можете узнать, как просмотреть и применить рекомендации по производительности для помощника по работе с Базами данных SQL. |
| [Просмотр запросов, максимально использующих ресурсы процессора](sql-database-query-performance.md#review-top-cpu-consuming-queries)| В этом руководстве вы можете узнать, как использовать анализ производительности запросов в помощнике по работе с Базами данных SQL для просмотра запросов, наиболее активно использующих ресурсы процессора.|
| [Просмотр подробных сведений для отдельного запроса](sql-database-query-performance.md#viewing-individual-query-details)| В этом руководстве вы можете узнать, как использовать анализ производительности запросов в помощнике по работе с Базами данных SQL для просмотра сведений о производительности отдельного запроса.|
||||

## Миграция и архивация Баз данных SQL 

Приведенные ниже руководства описывают [миграцию существующей базы данных SQL Server в Базу данных SQL Azure](sql-database-cloud-migrate.md).

| Учебник | Описание |
|---|---|---|
| [Обнаружение проблем совместимости с помощью SQL Server Data Tools для Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | В этом руководстве вы можете узнать, как использовать SQL Server Data Tools для Visual Studio, чтобы определить совместимость базы данных SQL Azure. |
| [Устранение проблем совместимости с помощью SQL Server Data Tools для Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | В этом руководстве вы можете узнать, как использовать SQL Server Data Tools для Visual Studio, чтобы исправить проблемы с совместимостью базы данных SQL Azure. |
| [Определение совместимости Базы данных SQL с помощью SqlPackage.exe](ql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | В этом руководстве вы можете узнать, как использовать программу командной строки SQLPackage.exe, чтобы определить совместимость базы данных SQL Azure.|
| [Определение совместимости Базы данных SQL с помощью SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |В этом руководстве вы можете узнать, как использовать SQL Server Management Studio, чтобы определить совместимость базы данных SQL Azure.|
| [Миграция базы данных SQL Server в Базу данных SQL с помощью мастера развертывания базы данных в Базу данных Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | В этом руководстве вы можете узнать, как перенести совместимую базу данных SQL Server в базу данных SQL Azure с помощью мастера развертывания базы данных в базу данных Microsoft Azure в среде SQL Server Management Studio.
| [Экспорт базы данных SQL Server в BACPAC-файл с помощью SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | В этом руководстве вы можете узнать, как экспортировать совместимую базу данных SQL Server в BACPAC-файл с помощью мастера экспорта приложений уровня данных в среде SQL Server Management Studio.|
| [Экспорт базы данных SQL Server в BACPAC-файл с помощью SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | В этом руководстве вы можете узнать, как экспортировать совместимую базу данных SQL Server в BACPAC-файл с помощью программы командной строки SQLPackage.exe.|
| [Импорт BACPAC-файла в Базу данных SQL Azure с помощью PowerShell](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | В этом руководстве вы можете узнать, как импортировать базу данных SQL Azure из BACPAC-файла с помощью мастера экспорта приложений уровня данных в среде SQL Server Management Studio. |
| [Импорт BACPAC-файла в Базу данных SQL Azure с помощью SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | В этом руководстве вы можете узнать, как импортировать базу данных SQL Azure из BACPAC-файла с помощью программы командной строки SQLPackage. |
| [Импорт BACPAC-файла в Базу данных SQL Azure с помощью портала Azure](sql-database-import.md) | В этом руководстве вы можете узнать, как импортировать базу данных SQL Azure из BACPAC-файла, хранящегося в BLOB-объекте Azure, с помощью портала Azure.|
| [Импорт BACPAC-файла в Базу данных SQL Azure с помощью PowerShell](sql-database-import-powershell.md) | В этом руководстве вы можете узнать, как импортировать базу данных SQL Azure из BACPAC-файла с помощью PowerShell.|
| [Архивация Базы данных SQL Azure с помощью портала Azure](sql-database-export.md#export-your-database) | В этом руководстве вы можете узнать, как заархивировать базу данных SQL Azure в BACPAC-файл с помощью портала Azure. |
| [Архивация Базы данных SQL Azure с помощью PowerShell](sql-database-export-powershell.md) | В этом руководстве вы можете узнать, как заархивировать базу данных SQL Azure в BACPAC-файл с помощью портала Azure. |
| [Копирование Базы данных SQL Azure с помощью портала Azure](sql-database-copy.md#copy-your-sql-database) | В этом руководстве вы можете узнать, как скопировать базу данных SQL Azure с помощью портала Azure. |
| [Копирование базы данных SQL Azure с помощью PowerShell](sql-database-copy-powershell#copy-your-sql-database) | В этом руководстве вы можете узнать, как скопировать базу данных SQL Azure с помощью PowerShell. |
| [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | В этом руководстве вы можете узнать, как скопировать базу данных SQL Azure с помощью Transact-SQL. |
||||

##Разработка

В следующих руководствах вы можете узнать о [подключении клиента к базе данных SQL Azure](sql-database-connect-central-recommendations.md) и использовании [библиотек подключений](sql-database-libraries.md).

| Учебник | Описание |
|---|---|---|
| [Подключение к базе данных SQL с помощью .NET (C#)](sql-database-develop-dotnet-simple.md) | В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью C#. |
| [Подключение к Базе данных SQL с помощью Java](sql-database-develop-java-simple.md) | В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью Java. |
| [Подключение к базе данных SQL с помощью Node.js](sql-database-develop-nodejs-simple.md) | В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью Node.js. |
| [Подключение к Базе данных SQL с помощью PHP](sql-database-develop-php-simple.md) | В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью PHP. |
| [Подключение к базе данных SQL с помощью Python](sql-database-develop-python-simple.md) | В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью Python. |
| [Подключение к базе данных SQL с помощью Ruby](sql-database-develop-ruby-simple.md) | В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью Ruby. |
||||
 
## Доступ к базе данных

В следующих руководствах вы можете узнать, как [создавать имена для входа и пользователей и управлять ими](sql-database-manage-logins.md).

| Учебник | Описание |
|---|---|---|
| [Создание правила брандмауэра серверного уровня для Базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md) | В этом руководстве вы можете узнать, как настроить брандмауэр серверного уровня для базы данных SQL с помощью портала Azure. |
| [Создание правила брандмауэра уровня базы данных с помощью Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | В этом руководстве вы можете узнать, как создать правило брандмауэра уровня базы данных с помощью Transact-SQL.|
| [Управление правилами брандмауэра на серверном уровне с помощью Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | В этом руководстве вы можете узнать, как управлять брандмауэром серверного уровня для базы данных SQL Azure с помощью Transact-SQL.|
| [Управление правилами брандмауэра на серверном уровне с помощью PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | В этом руководстве вы можете узнать, как управлять брандмауэром серверного уровня для базы данных SQL Azure с помощью PowerShell.|
| [Управление правилами брандмауэра на серверном уровне с помощью REST API](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | В этом руководстве вы можете узнать, как управлять брандмауэром серверного уровня для базы данных SQL Azure с помощью REST API.|
| [Подключение к базе данных SQL Azure с использованием имени субъекта на уровне сервера](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью имени субъекта на серверном уровне.|
| [Предоставление доступа к базе данных для имени входа](sql-database-manage-logins.md#granting-database-access-to-a-login() | В этом руководстве вы можете узнать, как предоставить доступ к базе данных для имени входа серверного уровня.|
| [Создание нового пользователя базы данных с помощью SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | В этом руководстве вы можете узнать, как создать нового пользователя базы данных в существующей базе данных с помощью SSMS.|
| [Предоставление новому пользователю базы данных разрешений db\_owner](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | В этом руководстве вы можете узнать, как предоставить существующему пользователю базы данных разрешения db\_owner.|
| [Подключение пользователя к базе данных SQL Azure](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | В этом руководстве вы можете узнать, как подключиться к базе данных SQL Azure с помощью имени пользователя на уровне базы данных.|
||||


## Защита данных

В следующих руководствах вы можете узнать о [защите данных в Базе данных SQL Azure](sql-database-security.md).

| Учебник | Описание |
|---|---|---|
| [Включение обнаружения угроз для базы данных с помощью портала Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | В этом руководстве вы можете узнать, как настроить обнаружение угроз для базы данных на портале Azure.|
| [Защита конфиденциальных данных с помощью постоянного шифрования](sql-database-always-encrypted-azure-key-vault.md) | В этом руководстве вы можете узнать, как использовать мастер постоянного шифрования для защиты конфиденциальных данных в Базе данных SQL Azure.|
| [Защита конфиденциальных данных с помощью прозрачного шифрования](https://msdn.microsoft.com/library/dn948096.aspx)| В этом руководстве вы можете узнать, как использовать прозрачное шифрование для защиты конфиденциальных данных.|
| [Шифрование столбца данных](https://msdn.microsoft.com/library/ms179331.aspx)| В этом руководстве вы можете узнать, как зашифровать столбец данных с помощью Transact-SQL.|
| [Настройка динамического маскирования данных](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) | В этом руководстве вы можете узнать, как настроить динамическое маскирование данных для базы данных SQL Azure. |
||||

## Непрерывность бизнес-процессов и масштабирование запросов

В следующих руководствах вы можете узнать об использовании [геовосстановления и активной георепликации](sql-database-business-continuity.md) для восстановления после ошибок, обеспечения непрерывности бизнес-процессов и масштабирования запросов.

| Учебник | Описание |
|---|---|---|
| [Восстановление базы данных SQL Azure до момента времени в прошлом с помощью портала Azure](sql-database-point-in-time-restore-portal.md)| В этом руководстве вы можете узнать, как восстановить базу данных до момента времени в прошлом с помощью портала Azure.|
| [Восстановление базы данных SQL Azure до точки во времени с помощью PowerShell](sql-database-point-in-time-restore-powershell.md) | В этом руководстве вы можете узнать, как восстановить базу данных до момента времени в прошлом с помощью PowerShell.|
| [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-portal.md) | В этом руководстве вы можете узнать, как восстановить удаленную базу данных с помощью портала Azure.|
| [Восстановление удаленной Базы данных SQL Azure с помощью PowerShell](sql-database-restore-deleted-database-powershell.md) | В этом руководстве вы можете узнать, как восстановить удаленную базу данных с помощью PowerShell.|
| [Настройка георепликации для базы данных SQL Azure с помощью портала Azure](sql-database-geo-replication-portal.md)| В этом руководстве вы можете узнать, как настроить активную георепликацию с помощью портала Azure.|
| [Настройка георепликации для Базы данных SQL Azure с помощью PowerShell](sql-database-geo-replication-powershell.md)| В этом руководстве вы можете узнать, как настроить активную георепликацию с помощью Transact-SQL.|
| [Настройка георепликации баз данных SQL Azure с помощью Transact-SQL.](sql-database-geo-replication-transact-sql.md)| В этом руководстве вы можете узнать, как настроить активную георепликацию с помощью Transact-SQL.|
| [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure](sql-database-geo-replication-failover-portal.md) | В этом руководстве вы можете узнать, как выполнить отработку отказа на геореплицированную вторичную реплику с помощью портала Azure.|
| [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью PowerShell](sql-database-geo-replication-failover-powershell.md) | В этом руководстве вы можете узнать, как выполнить отработку отказа на геореплицированную вторичную реплику с помощью PowerShell.|
| [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | В этом руководстве вы можете узнать, как выполнить отработку отказа на геореплицированную вторичную реплику с помощью Transact-SQL.|
||||

## Синхронизация данных

В этом руководстве вы можете узнать о [синхронизации данных](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Учебник | Описание |
|---|---|---| 
| [Приступая к работе с синхронизацией данных SQL Azure (предварительная версия)](sql-database-get-started-sql-data-sync.md) | В этом учебнике вы ознакомитесь с основами синхронизации данных SQL Azure с использованием классического портала Azure. |
||||

<!---HONumber=AcomDC_0518_2016-->