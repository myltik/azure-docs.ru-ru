<properties 
   pageTitle="Восстановление базы данных SQL Azure с использованием географического восстановления в API REST" 
   description="Географическое восстановление, база данных SQL Microsoft Azure, восстановление базы данных, восстановление базы данных, API REST" 
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
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# Восстановление базы данных SQL Azure с использованием географического восстановления в API REST

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)

## Обзор

В этом учебнике показано, как восстановить базу данных SQL Azure с помощью API REST. Здесь приведены ссылки на более подробную информацию об операциях. Географическое восстановление — это основной механизм защиты для аварийного восстановления баз данных SQL Azure на всех уровнях обслуживания — Basic, Standard и Premium.

## Ограничения и безопасность

См. [Восстановление базы данных SQL Azure с использованием географического восстановления на портале Azure](sql-database-geo-restore-tutorial-management-portal.md).

## Практическое руководство. Восстановление базы данных SQL Azure с использованием REST API

1.	Получите список баз данных, доступных для восстановления, используя операцию [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx)
	
2.	Получите базу данных, которую необходимо восстановить, используя операцию [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Создайте запрос на восстановление, используя операцию [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Отслеживайте состояние восстановления, используя операцию [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Дальнейшие действия

Дополнительные сведения см. в следующих разделах.

[Восстановление базы данных SQL Azure с использованием восстановления на момент времени на портале Azure](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Обеспечение непрерывности работы базы данных SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Резервное копирование и восстановление баз данных SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Географическое восстановление баз данных SQL Azure (блог)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Справочник по API-интерфейсу управления службами REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=62-->