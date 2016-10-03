<properties
   pageTitle="Запросы к хранилищу данных SQL Azure (Visual Studio) | Microsoft Azure"
   description="Отправка запросов к хранилищу данных SQL с помощью Visual Studio."
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
   ms.author="sonyama;barbkess"/>

# Запросы к хранилищу данных SQL Azure (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

В Visual Studio можно отправлять запросы к хранилищу данных SQL Azure за считанные минуты. В этом методе используется расширение SQL Server Data Tools (SSDT) в Visual Studio.

## Предварительные требования

Для работы с этим руководством необходимы указанные ниже компоненты.

+ Существующее хранилище данных SQL. Чтобы создать его, см. статью [Создание хранилища данных SQL Azure][].
+ Расширение SSDT для Visual Studio. Скорее всего, оно уже есть, если на вашем компьютере установлено приложение Visual Studio. Инструкции по установке и доступные варианты установки см. в статье [Установка Visual Studio 2015 и SSDT для хранилища данных SQL][].
+ Полное имя сервера SQL Server. Чтобы найти его, см. статью [Connect to Azure SQL Data Warehouse][] \(Подключение к хранилищу данных SQL Azure).

## 1\. Подключение к хранилищу данных SQL

1. Откройте Visual Studio 2013 или 2015.
2. Откройте обозреватель объектов SQL Server. Чтобы сделать это, последовательно выберите элементы **Представление** > **Обозреватель объектов SQL Server**.

    ![Обозреватель объектов SQL Server][1]

3. Щелкните значок **Добавить SQL Server**.

    ![Добавление SQL Server][2]

4. Заполните поля в окне «Подключение к серверу».

    ![Подключение к серверу][3]

    - **Имя сервера**. Введите найденное **имя сервера**.
    - **Проверка подлинности**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
    - **Имя пользователя** и **Пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
    - Щелкните **Подключить**.

5. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.

    ![Обзор AdventureWorksDW][4]

## 2\. Запуск пробного запроса

Теперь, когда мы подключились к базе данных, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.

2. Выберите пункт **Создать запрос**. Откроется окно нового запроса.

    ![Новый запрос][5]

3. Скопируйте следующий запрос TSQL в окно запроса.

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Выполните запрос. Для этого щелкните зеленую стрелку или воспользуйтесь сочетанием клавиш `CTRL`+`SHIFT`+`E`.

    ![Выполнение запроса][6]

5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.

    ![Результаты запроса][7]

## Дальнейшие действия

Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью PowerBI][].

Сведения о том, как настроить в своей среде проверку подлинности Azure Active Directory, см. в статье [Authentication to Azure SQL Data Warehouse][] \(Проверка подлинности в хранилище данных SQL Azure).

<!--Arcticles-->
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Создание хранилища данных SQL Azure]: sql-data-warehouse-get-started-provision.md
[Установка Visual Studio 2015 и SSDT для хранилища данных SQL]: sql-data-warehouse-install-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md
[визуализировать данные с помощью PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png

<!---HONumber=AcomDC_0622_2016-->