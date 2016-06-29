<properties
   pageTitle="Запросы к хранилищу данных SQL Azure (sqlcmd) | Microsoft Azure"
   description="Выполнение запросов к хранилищу данных SQL Azure с помощью служебной программы командной строки sqlcmd."
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
   ms.date="06/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Запросы к хранилищу данных SQL Azure (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

В этом пошаговом руководстве показано, как создавать запросы к хранилищу данных SQL Azure с помощью служебной программы командной строки sqlcmd.

## Предварительные требования

Для выполнения этих действий необходимо иметь следующее:

-  Файл [sqlcmd.exe][]. Чтобы скачать его, перейдите на страницу [Microsoft Command Line Utilities 11 для SQL Server][].

## 1\. Подключение

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

## 2\. Запрос

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
[connecting with PowerBI]: sql-data-warehouse-integrate-power-bi.md


<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/library/ms162773.aspx
[Microsoft Command Line Utilities 11 для SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portal]: https://portal.azure.com

<!--Image references-->

<!---HONumber=AcomDC_0622_2016-->