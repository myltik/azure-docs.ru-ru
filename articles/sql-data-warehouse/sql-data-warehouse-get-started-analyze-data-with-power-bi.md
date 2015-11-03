<properties
    pageTitle="Визуализация данных хранилища данных SQL с помощью Power BI | Microsoft Azure"
    description="Визуализация данных хранилища данных SQL с помощью Power BI"
    services="sql-data-warehouse"
    documentationCenter="NA"
    authors="lodipalm"
    manager="barbkess"
    editor=""/>

<tags
    ms.service="sql-data-warehouse"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="10/21/2015"
    ms.author="lodipalm"/>

# Визуализация данных с помощью Power BI

В этом руководстве показано, как использовать Power BI для подключения к хранилищу данных SQL и создания нескольких базовых визуализаций.

> [AZURE.NOTE]Для работы с этим руководством необходима база данных хранилища данных SQL, в которую предварительно загружена демонстрационная база данных AdventureWorksDW. В разделе [Создание хранилища данных SQL](sql-data-warehouse-get-started-provision.md) показано, как создать ее.
> 
> Если база данных хранилища данных SQL уже существует, но не содержит демонстрационных данных, вы можете [загрузить демонстрационные данные вручную][].

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## Подключение к AdventureWorksDW

Чтобы открыть Power BI и подключиться к базе данных AdventureWorksDW, выполните следующие действия.

1. Войдите на [портал предварительной версии Azure][].
2. Щелкните **Базы данных SQL** и выберите базу данных хранилища данных SQL AdventureWorks. 

    ![Поиск базы данных][1]

3. Нажмите кнопку «Открыть в Power BI».

    ![Кнопка Power BI][2]

4. Должна появиться страница подключения к хранилищу данных SQL с веб-адресом вашей базы данных. Нажмите кнопку «Далее».

    ![Power BI: подключение][3]

6. Введите имя пользователя и пароль сервера Azure SQL Server, чтобы полностью подключиться к базе данных хранилища данных SQL.

    ![Power BI: вход][4]

1. Войдя в службу Power BI и щелкните набор данных AdventureWorksDW в колонке слева. Откроется база данных.

    ![Power BI: открытие AdventureWorksDW][5]



## Создание отчета Power BI для анализа демонстрационных данных

Теперь вы готовы к использованию Power BI для анализа демонстрационных данных AdventureWorksDW. Для выполнения анализа в AdventureWorksDW предусмотрено представление с именем AggregateSales. Это представление содержит несколько основных метрик для анализа продаж компании.

1. Чтобы создать карту суммы продаж по почтовому индексу, разверните представление AggregateSales, щелкнув его в области полей справа. Выберите столбцы PostalCode и SalesAmount. 

    ![Power BI: выбор AggregateSales][6]

    Power BI в автоматическом режиме распознает географические данные и наносит их на карту.

    ![Карта Power BI][7]

2. На этом этапе создается линейчатая диаграмма, на которой отображается сумма продаж в зависимости от доходов клиентов. Чтобы создать эту диаграмму, перейдите к развернутому представлению AggregateSales. Щелкните поле SalesAmount. Перетащите поле «Доход клиентов» влево и поместите его на оси.
    
    ![Power BI: выбор оси][8]

    Мы переместили линейчатую диаграмму влево.

    ![Гистограмма Power BI][9]

3. На этом этапе создается график, на котором отображены продажи по дате заказа. Чтобы создать этот график, перейдите к развернутому представлению AggregateSales. Щелкните SalesAmount и OrderDate. В столбце «Визуализации» щелкните значок «График» (это первый значок во второй строке под заголовком «Визуализации»).

	![Power BI: выбор графика][10]

    Теперь у вас есть отчет, на котором отображены три разные визуализации.

    ![График Power BI][11]

Ход выполнения можно сохранить в любой момент, выбрав в меню **Файл** пункт **Сохранить**.

## Дальнейшие действия
Теперь, когда вы разобрались с образцом данных, узнайте, как [разрабатывать хранилища данных][], [загружать данные][] и [переносить готовые решения][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-connect-to-azure.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-sign-in.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-open-adventureworks.png
[6]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-aggregatesales.png
[7]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[8]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-chooseaxis.png
[9]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[10]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-prepare-line.png
[11]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png
[12]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-save.png

<!--Article references-->
[переносить готовые решения]: ./sql-data-warehouse-overview-migrate.md
[разрабатывать хранилища данных]: ./sql-data-warehouse-overview-develop.md
[загружать данные]: ./sql-data-warehouse-overview-load.md
[загрузить демонстрационные данные вручную]: ./sql-data-warehouse-get-started-manually-load-samples.md
[портал предварительной версии Azure]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: ./sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Nov15_HO1-->