<properties
   pageTitle="Начало работы. Подключение к хранилищу данных Azure SQL | Microsoft Azure"
   description="Начало работы. Подключение к хранилищу данных SQL и выполнение запросов."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Подключение и создание запросов с помощью Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

В этом пошаговом руководстве показано, как всего за несколько минут можно создать базу данных хранилища данных SQL Azure с помощью Visual Studio. В этом пошаговом руководстве описаны следующие операции.

+ Установка необходимого программного обеспечения.
+ Подключение к базе данных, содержащей образец базы данных AdventureWorksDW.
+ Выполнение запроса к образцу базы данных.  

## Предварительные требования

+ Visual Studio 2013/2015 — информацию о загрузке и установке Visual Studio 2015 и/или SSDT см. в статье [Установка Visual Studio и SSDT](sql-data-warehouse-install-visual-studio.md).

## Получение полного имени сервера Azure SQL

Для подключения к базе данных требуется полное имя сервера ( ***имя\_сервера**.database.windows.net* ), содержащее базу данных, к которой необходимо подключиться.

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Перейдите к базе данных, к которой нужно подключиться.
3. Найдите полное имя сервера (мы будем использовать его на следующих этапах).

![][1]

## Подключение к базе данных SQL

1. Откройте Visual Studio.
2. В меню «Просмотр» выберите пункт **Обозреватель объектов SQL Server**.

![][2]

3. Нажмите кнопку **Добавить SQL Server**.

![][3]

4. Введите *имя сервера*, которое упоминалось выше.
5. В списке **Проверка подлинности** выберите пункт **Проверка подлинности SQL Server**.
6. Введите **имя пользователя** и **пароль**, указанные при создании сервера базы данных SQL, и нажмите кнопку **Подключить**.

## Запуск образцов запросов

Теперь, когда мы зарегистрировали сервер, давайте напишем запрос.

1. Щелкните пользовательскую базу данных в SSDT.

2. Нажмите кнопку **Создать запрос**. Откроется новое окно.

![][4]

3. Введите следующий код в окне запроса:

	``sql
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Выполните запрос.

	Чтобы выполнить запрос, щелкните зеленую стрелку или воспользуйтесь сочетанием клавиш `CTRL`+`SHIFT`+`E`.

## Дальнейшие действия

Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [подключиться к PowerBI][].

[подключиться к PowerBI]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect-query/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect-query/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect-query/connection-dialog.png
[4]: ./media/sql-data-warehouse-get-started-connect-query/new-query.png

<!---HONumber=AcomDC_0330_2016-->