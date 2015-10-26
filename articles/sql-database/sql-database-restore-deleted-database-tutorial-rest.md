<properties 
   pageTitle="Восстановление удаленной базы данных SQL Azure с использованием API REST" 
   description="База данных SQL Microsoft Azure, восстановление удаленной базы данных, восстановление базы данных, API REST" 
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
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal"/>

# Восстановление удаленной базы данных SQL Azure с использованием API REST

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md) 

## Обзор

В этом учебнике показано, как восстановить удаленную базу данных SQL Azure с помощью API REST. Здесь приведены ссылки на более подробную информацию об операциях.

При восстановлении удаленной базы данных SQL Azure создается новая база данных. Служба автоматически выбирает уровень обслуживания на основе резервной копии, используемой в точке восстановления. Убедитесь, что на логическом сервере есть доступная квота для создания еще одной базы данных. Если вы хотите запросить более высокую квоту, обратитесь в [службу поддержки Azure](http://azure.microsoft.com/support/options/).

## Ограничения и безопасность

См. статью [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-tutorial-management-portal.md).

## Практическое руководство. Восстановление удаленной базы данных SQL Azure с использованием REST API

1.	Создайте список всех своих удаленных баз данных, которые можно восстановить, используя операцию [List Restorable Dropped Databases](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Получите подробную информацию об удаленной базе данных, которую необходимо восстановить, используя операцию [Get Restorable Dropped Database](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Начните восстановление, используя операцию [Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Отследите состояние восстановления, используя операцию [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Дальнейшие действия

Дополнительные сведения см. в следующих разделах.

[Обеспечение непрерывности работы базы данных SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Резервное копирование и восстановление баз данных SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Справочник по API-интерфейсу управления службами REST](http://msdn.microsoft.com/library/azure/ee460799.aspx)

<!---HONumber=Oct15_HO3-->