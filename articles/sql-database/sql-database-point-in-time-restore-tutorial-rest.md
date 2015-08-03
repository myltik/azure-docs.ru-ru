<properties 
   pageTitle="Восстановление базы данных SQL Azure с использованием восстановления на момент времени с помощью API REST" 
   description="Восстановление на момент времени, база данных SQL Microsoft Azure, восстановление базы данных, восстановление базы данных, API REST" 
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

# Восстановление базы данных SQL Azure с использованием восстановления на момент времени с помощью API REST

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md) 

## Обзор

В этом учебнике показано восстановление базы данных SQL Azure с использованием восстановления на момент времени с помощью API REST. Здесь приведены ссылки на более подробную информацию об операциях.

При восстановлении на момент времени создается новая база данных. Служба автоматически выбирает уровень обслуживания на основе резервной копии, используемой в точке восстановления. Убедитесь, что на логическом сервере есть доступная квота для создания еще одной базы данных. Если вы хотите запросить более высокую квоту, обратитесь в [службу поддержки Azure](http://azure.microsoft.com/support/options/).

## Ограничения и безопасность

См. раздел [Восстановление базы данных SQL Azure с использованием восстановления на момент времени на портале Azure](sql-database-point-in-time-restore-tutorial-management-portal.md).

## Практическое руководство. Восстановление базы данных SQL Azure с использованием REST API

1.	Получите базу данных, которую необходимо восстановить, используя операцию [Get Database](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Создайте запрос на восстановление, используя операцию [Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Отследите запрос на восстановление, используя операцию [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Дальнейшие действия

Дополнительные сведения см. в следующих разделах.

[Обеспечение непрерывности работы базы данных SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Резервное копирование и восстановление баз данных SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Восстановление базы данных SQL Azure на определенный момент времени (блог)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Справочник по API-интерфейсу управления службами REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=July15_HO4-->