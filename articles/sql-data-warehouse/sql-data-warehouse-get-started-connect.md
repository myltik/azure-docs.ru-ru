<properties
   pageTitle="Подключение к хранилищу данных SQL с помощью Visual Studio | Microsoft Azure"
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
   ms.date="01/04/2016"
   ms.author="twounder;barbkess"/>

# Подключение к хранилищу данных SQL с помощью Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

В этом пошаговом руководстве показано, как всего за несколько минут подключиться к базе данных хранилища данных SQL Azure с помощью набора SQL Server Data Tools в Visual Studio. После подключения вы сможете выполнить пробный запрос.

## Предварительные требования

+ Образец базы данных AdventureWorksDW в хранилище данных SQL. Чтобы создать его, см. статью [Создание базы данных в хранилище данных SQL](sql-data-warehouse-get-started-create.md). 
+ SQL Server Data Tools для Visual Studio. Инструкции по установке и параметры см. в статье [Установка Visual Studio и (или) SSDT](sql-data-warehouse-install-visual-studio.md).

## Этап 1. Поиск полного имени сервера Azure SQL Server

Ваша база данных связана с сервером Azure SQL Server. Для подключения к базе данных требуется полное имя сервера (**имя\_сервера**.database.windows.net*).

Чтобы найти полное имя сервера, сделайте следующее.

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Базы данных SQL** и выберите базу данных, к которой необходимо подключиться. В этом примере используется образец базы данных AdventureWorksDW.
3. Найдите полное имя сервера.

    ![Полное имя сервера][1]

## Этап 2. Подключение к базе данных SQL

1. Откройте Visual Studio.
2. Откройте обозреватель объектов SQL Server. Чтобы сделать это, последовательно выберите элементы **Представление** > **Обозреватель объектов SQL Server**.
 
    ![Обозреватель объектов SQL Server][2]

3. Щелкните значок **Добавить SQL Server**.

    ![Добавление SQL Server][3]

1. Заполните поля в окне «Подключение к серверу».

    ![Подключение к серверу][4]

    - **Имя сервера**. Введите *имя сервера*, найденное выше.
    - **Проверка подлинности**. Выберите Аутентификация на SQL-сервере.
    - **Имя для входа** и **Пароль**. Введите имя пользователя и пароль для сервера Azure SQL Server.
    - Щелкните **Подключить**.

1. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.

    ![Обзор AdventureWorksDW][5]


## Этап 3. Запуск пробного запроса

Теперь, когда мы подключились к серверу, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server. 

2. Выберите пункт **Создать запрос**. Откроется окно нового запроса.

    ![Новый запрос][6]

3. Скопируйте следующий запрос TSQL в окно запроса.

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Выполните запрос. Для этого щелкните зеленую стрелку или воспользуйтесь сочетанием клавиш `CTRL`+`SHIFT`+`E`.

    ![Выполнение запроса][7]

1. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.

    ![Результаты запроса][8]

## Дальнейшие действия

Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью PowerBI][].

[визуализировать данные с помощью PowerBI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0107_2016-->