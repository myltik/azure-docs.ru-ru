<properties
   pageTitle="Подключение к хранилищу данных SQL с помощью Visual Studio | Microsoft Azure"
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
   ms.date="06/09/2016"
   ms.author="sonyama;barbkess"/>

# Подключение к хранилищу данных SQL с помощью Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)

Из этого пошагового руководства вы узнаете, как всего за несколько минут подключиться к хранилищу данных SQL Azure с помощью средств SQL Server Data Tools (SSDT) в Visual Studio. После подключения вы сможете выполнить пробный запрос.

## Предварительные требования

+ Пример данных AdventureWorksDW в хранилище данных SQL. См. статью [Создание хранилища данных SQL][].
+ SQL Server Data Tools для Visual Studio. Инструкции по установке и доступные варианты установки см. в статье [Установка Visual Studio 2015 и (или) SSDT для хранилища данных SQL][].

## Этап 1. Поиск полного имени сервера Azure SQL Server

Ваша база данных в хранилище данных SQL связана с сервером SQL Server в Azure. Чтобы подключиться к базе данных, вам потребуется полное доменное имя сервера (**имя\_сервера**.database.windows.net*).

Чтобы найти полное имя сервера, сделайте следующее.

1. Перейдите на [портал Azure][].
2. Щелкните **Базы данных SQL** и выберите базу данных, к которой необходимо подключиться. В этом примере используется образец базы данных AdventureWorksDW.
3. Найдите полное имя сервера.

    ![Полное имя сервера][1]

## Этап 2. Подключение к базе данных в хранилище данных SQL

1. Откройте Visual Studio 2013 или 2015.
2. Откройте обозреватель объектов SQL Server. Чтобы сделать это, последовательно выберите элементы **Представление** > **Обозреватель объектов SQL Server**.

    ![Обозреватель объектов SQL Server][2]

3. Щелкните значок **Добавить SQL Server**.

    ![Добавление SQL Server][3]

4. Заполните поля в окне «Подключение к серверу».

    ![Подключение к серверу][4]

    - **Имя сервера**. Введите найденное **имя сервера**.
    - **Проверка подлинности**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
    - **Имя пользователя** и **Пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
    - Щелкните **Подключить**.

5. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.

    ![Обзор AdventureWorksDW][5]

## Этап 3. Запуск пробного запроса

Теперь, когда мы подключились к базе данных, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.

2. Выберите пункт **Создать запрос**. Откроется окно нового запроса.

    ![Новый запрос][6]

3. Скопируйте следующий запрос TSQL в окно запроса.

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Выполните запрос. Для этого щелкните зеленую стрелку или воспользуйтесь сочетанием клавиш `CTRL`+`SHIFT`+`E`.

    ![Выполнение запроса][7]

5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.

    ![Результаты запроса][8]

## Дальнейшие действия

Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью PowerBI][].

Сведения о том, как настроить в своей среде проверку подлинности Windows, см. в статье [Подключение к Базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory][].

<!--Arcticles-->
[Создание хранилища данных SQL]: sql-data-warehouse-get-started-provision.md
[Установка Visual Studio 2015 и (или) SSDT для хранилища данных SQL]: sql-data-warehouse-install-visual-studio.md
[Подключение к Базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[визуализировать данные с помощью PowerBI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md

<!--Other-->
[портал Azure]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0615_2016-->