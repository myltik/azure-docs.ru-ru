<properties
   pageTitle="Выполнение запросов с использованием SQLCMD | Microsoft Azure"
   description="Отправка запросов к хранилищу данных с использованием SQLCMD."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/09/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Выполнение запросов с использованием SQLCMD

> [AZURE.SELECTOR]
- [Power BI][]
- [Машинное обучение Azure][]
- [SQLCMD][]

В этом руководстве показано, как отправлять запросы к хранилищу данных SQL Azure, используя служебную программу sqlcmd.exe.

## Предварительные требования

+ Чтобы скачать [sqlcmd.exe][], перейдите на страницу [Microsoft Command Line Utilities 11 для SQL Server][].

## Подключение

Чтобы начать использовать sqlcmd, откройте командную строку и введите **sqlcmd** и строку подключения к базе данных хранилища данных SQL. В строке подключения необходимо указать следующие обязательные параметры:

+ **Server (-S):** сервер в виде `<`имя сервера`>`.database.windows.net
+ **Database (-d)**. Имя базы данных.
+ **User (-U)** — пользователь сервера в формате `<`Пользователь`>`.
+ **Password (-P)** — пароль, связанный с пользователем.
+ **Enable Quoted Identifiers (-I)** — для подключения к экземпляру хранилища данных SQL необходимо включить заключенные в кавычки идентификаторы.

Например, строка подключения может выглядеть так:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] Параметр -I, который включает заключенные в кавычки идентификаторы, требуется для подключения к хранилищу данных SQL.

## Запрос

После подключения можно подавать любые поддерживаемые инструкции Transact-SQL для экземпляра. В этом примере запросы отправляются в интерактивном режиме.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

В следующих примерах показано, как выполнить запросы в пакетном режиме, используя параметр -Q или передав SQL программе sqlcmd.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
C:\>"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## Дальнейшие действия

Сведения обо всех параметрах sqlcmd см. в [документации по sqlcmd][sqlcmd.exe].

<!--Articles-->
[connecting with PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Машинное обучение Azure]: ./sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/ru-RU/library/ms162773.aspx
[Microsoft Command Line Utilities 11 для SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portal]: https://portal.azure.com

<!--Image references-->

<!---HONumber=AcomDC_0615_2016-->