<properties
   pageTitle="Начало работы. Подключение к хранилищу данных Azure SQL | Microsoft Azure"
   description="Начало работы. Подключение к хранилищу данных SQL и выполнение запросов."
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
   ms.date="05/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Подключение и создание запросов с помощью SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)


В этом пошаговом руководстве показано, как всего за несколько минут можно создать базу данных хранилища данных SQL Azure с помощью служебной программы sqlcmd.exe. В этом пошаговом руководстве описаны следующие операции.

+ Установка необходимого программного обеспечения.
+ Подключение к базе данных, содержащей образец базы данных AdventureWorksDW.
+ Выполнение запроса к образцу базы данных.  

## Предварительные требования

+ Чтобы скачать [sqlcmd.exe][], перейдите на страницу [Microsoft Command Line Utilities 11 для SQL Server][].

## Получение полного имени сервера Azure SQL

Для подключения к базе данных требуется полное имя сервера (****имя\_сервера**.database.windows.net*), содержащее базу данных, к которой необходимо подключиться.

1. Перейдите на [портал Azure][].
2. Перейдите к базе данных, к которой нужно подключиться.
3. Найдите полное имя сервера (мы будем использовать его на следующих этапах).

![][1]


## Подключение к хранилищу данных SQL с помощью sqlcmd

Чтобы подключиться к определенному экземпляру хранилища данных SQL с помощью sqlcmd, откройте командную строку и введите **sqlcmd** и строку подключения к базе данных хранилища данных SQL. В строке подключения необходимо указать следующие обязательные параметры:

+ **Server (-S):** сервер в виде `<`имя сервера`>`.database.windows.net
+ **Database (-d):** имя базы данных.
+ **User (-U)** — пользователь сервера в формате `<`Пользователь`>`.
+ **Password (-P)** — пароль, связанный с пользователем.
+ **Enable Quoted Identifiers (-I)** — для подключения к экземпляру хранилища данных SQL необходимо включить заключенные в кавычки идентификаторы.

Таким образом, для подключения к экземпляру хранилища данных SQL нужно ввести следующее:

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Запуск образцов запросов

После подключения можно подавать любые поддерживаемые инструкции Transact-SQL для экземпляра.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Дополнительные сведения о sqlcmd см. в [документации по sqlcmd][sqlcmd.exe].


## Дальнейшие действия

Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [подключиться к PowerBI][].

Сведения о том, как настроить в своей среде аутентификацию Windows, см. в статье [Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication][] (Подключение к хранилищу данных SQL с использованием проверки подлинности Azure Active Directory).

<!--Articles-->
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[подключиться к PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/ru-RU/library/ms162773.aspx
[Microsoft Command Line Utilities 11 для SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[портал Azure]: https://portal.azure.com

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

<!---HONumber=AcomDC_0601_2016-->