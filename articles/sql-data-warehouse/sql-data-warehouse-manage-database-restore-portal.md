<properties
   pageTitle="Восстановление базы данных в хранилище данных SQL Azure (портал Azure) | Microsoft Azure"
   description="Задачи портала Azure для восстановления работающей, удаленной или недоступной базы данных в хранилище данных SQL Azure."
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
   ms.date="06/02/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Архивация и восстановление базы данных в хранилище данных SQL Azure (портал Azure)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-overview-manage-database-restore.md)
- [Портал](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Задачи портала Azure для восстановления базы данных в хранилище данных SQL.

Задачи раздела.

- Восстановление активной базы данных.
- Восстановление удаленной базы данных.

## Перед началом работы

Проверьте ресурсы DTU Базы данных SQL. Так как восстановление хранилища данных SQL выполняется в новую базу данных на логическом сервере SQL Server, важно убедиться, что на используемом сервере имеется достаточно единиц DTU для новой базы данных. В этой записи блога вы можете узнать больше о том, [как просмотреть и увеличить квоту DTU][].


## Восстановление активной базы данных.

Процедура восстановления базы данных

1. Войдите на [портал Azure][].
2. В левой части экрана выберите **ОБЗОР**, а затем — **Базы данных SQL**.
3. Перейдите к нужной базе данных и выберите ее.
4. В верхней части колонки базы данных щелкните **Восстановить**.
5. Укажите новое значение в поле **Имя базы данных**, выберите **точку восстановления**, а затем нажмите кнопку **Создать**.
6. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **уведомлений**.


## Восстановление удаленной базы данных.

Восстановление удаленной базы данных.

1. Войдите на [портал Azure][].
2. В левой части экрана выберите **ОБЗОР**, а затем — **Серверы SQL**.
3. Перейдите к нужному серверу и выберите его.
4. Прокрутите вниз колонку сервера до раздела "Операции" и щелкните **Удаленные базы данных**.
5. Выберите удаленную базу данных, которую хотите восстановить.
5. Укажите новое значение в поле **Имя базы данных** и нажмите кнопку **Создать**.
6. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **уведомлений**.


## Дальнейшие действия
Дополнительные сведения см. в [обзоре обеспечения непрерывности бизнес-процессов в базе данных SQL Azure][] и [обзоре управления][].

<!--Image references-->

<!--Article references-->
[обзоре обеспечения непрерывности бизнес-процессов в базе данных SQL Azure]: sql-database-business-continuity.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md
[обзоре управления]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->

<!--Blog references-->
[как просмотреть и увеличить квоту DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[портал Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->