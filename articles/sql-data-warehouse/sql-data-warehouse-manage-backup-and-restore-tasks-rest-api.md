<properties
   pageTitle="Резервное копирование и восстановление базы данных в хранилище данных SQL Azure (REST) | Microsoft Azure"
   description="Задачи REST API для восстановления работающей, удаленной или недоступной базы данных в хранилище данных SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Резервное копирование и восстановление базы данных в хранилище данных SQL Azure (REST)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Портал](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

Создание резервной копии и восстановление базы данных в хранилище данных SQL с помощью интерфейсов REST API.

Задачи раздела

- Восстановление активной базы данных.
- Восстановление удаленной базы данных.
- Восстановление недоступной базы данных из другого географического региона Azure

[AZURE.INCLUDE [Политика хранения резервных копий хранилища данных SQL](../../includes/sql-data-warehouse-backup-retention-policy.md)]

## Перед началом работы

**Проверьте ресурсы DTU базы данных SQL.** Поскольку восстановление хранилища данных SQL выполняется в новую базу данных на логическом сервере SQL Server, важно убедиться, что на используемом сервере имеется достаточно единиц DTU для новой базы данных. В этой записи блога вы можете узнать больше о том, [как просмотреть и увеличить квоту DTU][].

## Восстановление активной базы данных.

Процедура восстановления базы данных

1. Получите список точек восстановления базы данных с помощью операции Get Database Restore Points.
2. Начните восстановление с помощью операции [Create Database Restore Request][].
3. Отследите состояние восстановления с помощью операции [Database Operation Status][].

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных][].

## Восстановление удаленной базы данных.

Процедура восстановления удаленной базы данных

1.	Создайте список всех удаленных баз данных, доступных для восстановления, с помощью операции [List Restorable Dropped Databases][].
2.	Получите подробную информацию об удаленной базе данных, которую необходимо восстановить, с помощью операции [Get Restorable Dropped Database][].
3.	Начните восстановление с помощью операции [Create Database Restore Request][].
4.	Отследите состояние восстановления с помощью операции [Database Operation Status][].

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных][].

## Географическое восстановление хранилища данных из геоизбыточной резервной копии

Процедура географического восстановления

1. Получите список баз данных, доступных для восстановления, используя операцию [List Recoverable Databases][].
2. Получите базу данных, которую необходимо восстановить, используя операцию [Get Recoverable Database][].
3. Создайте запрос на восстановление, используя операцию [Create Database Recovery Request][].
4. Отслеживайте состояние восстановления, используя операцию [Database Operation Status][].

### Настройка базы данных после выполнения географического восстановления
Далее приведен контрольный список задач, который можно использовать для подготовки восстановленной базы данных к использованию в рабочей среде.

1. **Обновление строк подключения**. Убедитесь, что строки подключения клиентских средств указывают на только что восстановленную базу данных.
2. **Изменение правил брандмауэра**. Проверьте правила брандмауэра на целевом сервере и убедитесь в том, что в них разрешены подключения клиентских компьютеров или Azure к серверу и только что восстановленной базе данных.
3. **Проверка имен для входа на сервер и пользователей базы данных**. Проверьте существование всех имен для входа, используемых вашим приложением, на сервере, на котором размещена восстановленная база данных. Повторно создайте отсутствующие имена для входа и предоставьте им соответствующие разрешения на работу с восстановленной базой данных. 
4. **Включение аудита**. Если для доступа к базе данных требуется аудит, то после восстановления базы данных его необходимо включить.

Восстановленная база данных будет поддерживать прозрачное шифрование данных, если исходная база данных поддерживает прозрачное шифрование данных.


## Дальнейшие действия
Дополнительные сведения о функциях обеспечения непрерывности бизнес-процессов в выпусках базы данных SQL Azure см. в [обзоре непрерывности бизнес-процессов базы данных Azure SQL][].

<!--Image references-->

<!--Article references-->
[обзоре непрерывности бизнес-процессов базы данных Azure SQL]: sql-database-business-continuity.md
[Финализация восстановленной базы данных]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[как просмотреть и увеличить квоту DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->