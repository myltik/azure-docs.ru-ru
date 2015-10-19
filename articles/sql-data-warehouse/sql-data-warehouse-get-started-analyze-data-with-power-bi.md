<properties
    pageTitle="Анализ данных в хранилище данных SQL с помощью Power BI | Microsoft Azure"
    description="Анализ данных в хранилище данных SQL с помощью Power BI"
    services="sql-data-warehouse"
    documentationCenter="NA"
    authors="lodipalm"
    manager="barbkess"
    editor=""/>

<tags
    ms.service="sql-data-warehouse"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# Анализ данных с помощью Power BI
В этом руководстве показано, как использовать Power BI для подключения к хранилищу данных SQL и создания нескольких базовых визуализаций.

> [AZURE.NOTE]Для работы с этим руководством необходима база данных хранилища данных SQL, в которую предварительно загружена демонстрационная база данных AdventureWorksDW. В разделе [Создание хранилища данных SQL](sql-data-warehouse-get-started-provision.md) показано, как создать ее.
> 
> Если база данных хранилища данных SQL уже присутствует, однако не содержит демонстрационных данных, можно [загрузить демонстрационные данные вручную][].


## Подключение к базе данных хранилища данных SQL

Чтобы открыть Power BI и подключиться к базе данных AdventureWorksDW, выполните следующие действия.

1. Выполните вход на [портал предварительной версии Azure][].
2. Щелкните **Базы данных SQL** и выберите базу данных хранилища данных SQL AdventureWorks. 

    ![Поиск базы данных][1]

3. Нажмите кнопку «Открыть в Power BI».

    ![Кнопка Power BI][2]

4. Должна появиться страница подключения к хранилищу данных SQL со сведениями о вашей базе данных. Введите пароль для окончательного подключения к базе данных хранилища данных SQL.


## Анализ данных с помощью Power BI

Теперь вы готовы к использованию Power BI для анализа демонстрационных данных AdventureWorksDW. Для выполнения анализа в AdventureWorksDW предусмотрено представление с именем AggregateSales. Это представление содержит несколько основных метрик для анализа продаж компании.

1. Чтобы создать карту объема продаж по почтовому индексу, щелкните представление AggregateSales, чтобы развернуть его, и затем щелкните столбцы PostalCode и SalesAmount. Power BI в автоматическом режиме распознает географические данные и наносит их на карту.

    ![Карта Power BI][3]

2. Чтобы создать гистограмму продаж, просто щелкните столбец SalesAmount. Чтобы добавить дополнительную информацию, перетащите диаграмму CustomerIncome в поле «Ось» слева от представления AggregateSales. На графике появится информация о продажах в зависимости от доходов клиентов.

    ![Гистограмма Power BI][4]

3. Чтобы создать временную шкалу продаж, щелкните столбцы SalesAmount и OrderDate, а затем щелкните значок «График» (первый значок во второй строке в разделе «Визуализация данных»).

    ![График Power BI][5]

Вы можете в любой момент сохранить свою работу, нажав в верхнем левом углу кнопку **Сохранить** и сохранив визуализации в виде отчета.

## Дальнейшие действия
Теперь, когда вы разобрались с образцом данных, узнайте, как [разрабатывать хранилища данных][], [загружать данные][] и [переносить готовые решения][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[переносить готовые решения]: sql-data-warehouse-overview-migrate.md
[разрабатывать хранилища данных]: sql-data-warehouse-overview-develop.md
[загружать данные]: sql-data-warehouse-overview-load.md
[загрузить демонстрационные данные вручную]: sql-data-warehouse-get-started-manually-load-samples.md
[портал предварительной версии Azure]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO2-->