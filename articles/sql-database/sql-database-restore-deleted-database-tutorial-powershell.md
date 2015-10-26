<properties 
   pageTitle="Восстановление удаленной базы данных SQL Azure в Azure PowerShell" 
   description="База данных SQL Microsoft Azure, восстановление удаленной базы данных, восстановление базы данных, Azure PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="10/08/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Восстановление удаленной базы данных SQL Azure в Azure PowerShell

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## Обзор

В этом учебнике показано, как восстановить удаленную базу данных SQL Azure в [Azure PowerShell](../powershell-install-configure.md). Вы можете восстановить базу данных, которая была удалена в течение срока хранения, до момента времени удаления. Срок хранения определяется по уровню обслуживания базы данных.

При восстановлении удаленной базы данных SQL Azure создается новая база данных. Служба автоматически выбирает уровень обслуживания на основе резервной копии, используемой в точке восстановления. Убедитесь, что на логическом сервере есть доступная квота для создания еще одной базы данных. Если вы хотите запросить более высокую квоту, обратитесь в [службу поддержки Azure](http://azure.microsoft.com/support/options/).

## Ограничения и безопасность

См. статью [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-tutorial-management-portal.md).

## Практическое руководство. Восстановление удаленной базы данных SQL Azure в Azure PowerShell

> [AZURE.VIDEO restore-a-deleted-sql-database-with-microsoft-azure-powershell]

Вам необходимо использовать аутентификацию на основе сертификата для выполнения следующих командлетов. Дополнительную информацию см. в разделе *Использование сертификата* в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md#use-the-certificate-method).

> [AZURE.IMPORTANT]Эта статья содержит команды для Azure PowerShell версии вплоть до 1.0, *но не включая* саму версию 1.0 и более поздние версии. Используемую версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**.

1. Получите список баз данных, которые можно восстановить, используя командлет [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx).
	* Используйте переключатель **RestorableDropped** и укажите значение параметра **ServerName** для сервера, с которого была удалена база данных.
	* При выполнении следующей команды результаты сохраняются в переменной с именем **$RecoverableDBs**.
	
	`$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" –RestorableDropped`

2. Выберите удаленную базу данных, которую требуется восстановить, в списке удаленных баз данных.

	* Введите номер удаленной базы данных из списка **$RecoverableDBs**.  

	`$Database = $RecoverableDBs[<deleted database number>]`

	* Дополнительную информацию о том, как получить объект удаленной базы данных, которую можно восстановить, см. в разделе [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx).

3. Начните восстановление, используя командлет [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Укажите следующие параметры:
	* **SourceRestorableDroppedDatabase**
	* **TargetDatabaseName** для имени базы данных, в которую необходимо выполнить восстановление.

	Сохраните результат, возвращенный переменной с именем **$RestoreRequest**. Эта переменная содержит идентификатор запроса восстановления, который используется для мониторинга состояния восстановления.
	
	`$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “myrestoredDB”`

Восстановление может занять некоторое время. Для мониторинга состояния восстановления используйте командлет [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) и укажите следующие параметры:

* **ServerName** для имени базы данных, в которую необходимо выполнить восстановление.
* **OperationGuid** для глобального уникального идентификатора, которым является идентификатор запроса восстановления, хранимый в переменной **$RestoreRequest** на шаге 3.

	`Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

В полях **State** и **PercentComplete** отображается состояние восстановления.

## Дальнейшие действия

Дополнительные сведения см. в следующих разделах.

[Обеспечение непрерывности работы базы данных SQL Azure](sql-database-business-continuity.md)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)

<!---HONumber=Oct15_HO3-->