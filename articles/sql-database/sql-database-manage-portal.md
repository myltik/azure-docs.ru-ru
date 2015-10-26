<properties 
	pageTitle="Управление Базами данных SQL Azure с помощью портала управления Azure" 
	description="Узнайте, как использовать портал управления Azure для управления реляционной базой данных в облаке с помощью портала управления Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="09/11/2015" 
	ms.author="sstein"/>


# Управление Базами данных SQL Azure с помощью портала управления Azure


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

[Портал управления Azure][Management Portal] позволяет создавать базы данных SQL Azure и серверы, выполнять их мониторинг, а также управлять ими. В данной статье рассматриваются операции с базой данных, которые можно выполнять с помощью портала управления.

>[AZURE.NOTE]Если вы еще не знакомы с порталом управления Azure, просмотрите этот [видеокурс с кратким обзором][Azure Portal Tour] основных возможностей портала и основных понятий.

![Обзор базы данных](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\. Операции управления базой данных
![Операции управления базой данных](./media/sql-database-manage-portal/sqldatabase_actions.png)

На портале управления Azure предоставлен набор наиболее часто используемых операций с базой данных, доступных в верхней колонке базы данных. Можно восстановить базу данных до более раннего состояния, открыть базу данных в Visual Studio, скопировать базу данных на новый сервер, а также экспортировать базу данных в учетную запись хранения Azure.

- [Восстановление базы данных SQL](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Открытие базы данных SQL в Visual Studio](sql-database-connect-query.md)
- [Экспорт базы данных SQL](sql-database-export.md)

## 2\. Мониторинг базы данных
![Мониторинг базы данных](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Базы данных SQL Azure по умолчанию включают диаграммы мониторинга для единицы пропускной способности базы данных (DTU), размера базы данных и состояния подключения. Вы можете настроить и расширить эти диаграммы мониторинга, дополнив их диаграммами с информацией о загрузке ЦП в процентах, выполнении операций ввода-вывода в процентах, сведениями о блокировках, выполнении операций ввода-вывода журнала в процентах или даже данными о проценте запросов, блокируемых брандмауэром. Дополнительные сведения о том, как настроить диаграммы мониторинга представлены [здесь][Azure part monitoring].

Кроме того, можно настроить правила оповещения, чтобы организовать наблюдение за указанным метриками и отправку назначенному администратору и соадминистратору соответствующих предупреждений при достижении заранее установленных пороговых значений. Дополнительные сведения о настройке правил оповещения на портале управления Azure представлены [здесь][Azure part monitoring].

## 3\. Безопасность и аудит базы данных
![Безопасность базы данных](./media/sql-database-manage-portal/sqldatabase_security.png)

Базы данных SQL Azure можно настроить для отслеживания всех событий базы данных и записи их в журнал аудита для вашей учетной записи хранения Azure. Это может помочь вам соблюсти требования нормативов, проанализировать работу с базой данных и получить представление о расхождениях, которые могут указывать на бизнес-проблемы или предполагаемые нарушения безопасности.

- [Аудит базы данных SQL](sql-database-auditing-get-started.md)

Базы данных SQL Azure также можно настроить на маскирование конфиденциальных данных для пользователей с недостаточными полномочиями.

- [динамическое маскирование данных](sql-database-dynamic-data-masking-get-started.md);


## 4\. Георепликация
![Георепликация](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Базы данных SQL Azure можно настроить на асинхронную репликацию зафиксированных транзакций в базу данных-получатель. С помощью функции георепликации, доступной на портале управления, можно выбирать регион Azure, в котором следует разместить базу данных-получатель.

- [Георепликация](https://msdn.microsoft.com/library/azure/dn783447.aspx)





##Дополнительные ресурсы
* [База данных SQL](sql-database-technical-overview.md)   
* [Мониторинг Базы данных SQL с помощью динамических административных представлений][]   
* [Справочник по Transact-SQL (База данных SQL)][]
  
  [Azure Portal Tour]: https://go.microsoft.com/fwlink/?LinkID=522341
  [Management Portal]: https://portal.azure.com
  [Azure part monitoring]: ../documentdb-monitor-accounts.md
  [AzureDb management overview]: http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/
  [Introducing SQL Database]: http://azure.microsoft.com/services/sql-database
  [Database geo-replication]: http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/
  [Managing Azure SQL Database using SQL Server Management Studio]: sql-database-manage-azure-ssms.md
  [Мониторинг Базы данных SQL с помощью динамических административных представлений]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Справочник по Transact-SQL (База данных SQL)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [AzureDb Auditing]: http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/
  [AzureDb datamasking]: http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/

 
 

<!---HONumber=Oct15_HO3-->