---
title: "Визуализация данных хранилища данных SQL с помощью Power BI | Microsoft Azure"
description: "Визуализация данных хранилища данных SQL с помощью Power BI"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 8790bacecac0fa824189b5c212c2d803092ff4ed
ms.lasthandoff: 01/30/2017


---
# <a name="visualize-data-with-power-bi"></a>Визуализация данных с помощью Power BI
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

В этом руководстве показано, как использовать Power BI для подключения к хранилищу данных SQL и создания нескольких базовых визуализаций.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для выполнения этих действий необходимо иметь следующее:

* Хранилище данных SQL, в которое предварительно загружена база данных AdventureWorksDW. Для подготовки см. статью [Создание хранилища данных SQL Azure][Create a SQL Data Warehouse] и выберите загрузку демонстрационных данных. Если хранилище данных уже существует, но в нем нет демонстрационных данных, вы можете [загрузить их вручную][load sample data manually].

## <a name="1-connect-to-your-database"></a>1. Подключение к базе данных
Чтобы открыть Power BI и подключиться к базе данных AdventureWorksDW, выполните следующие действия.

1. Войдите на [портал Azure][Azure portal].
2. Щелкните **Базы данных SQL** и выберите базу данных хранилища данных SQL AdventureWorks.
   
    ![Поиск базы данных][1]
3. Нажмите кнопку «Открыть в Power BI».
   
    ![Кнопка Power BI][2]
4. Должна появиться страница подключения к хранилищу данных SQL с веб-адресом вашей базы данных. Нажмите кнопку «Далее».
   
    ![Power BI: подключение][3]
5. Введите имя пользователя и пароль сервера Azure SQL Server, чтобы полностью подключиться к базе данных хранилища данных SQL.
   
    ![Power BI: вход][4]
6. Войдя в службу Power BI и щелкните набор данных AdventureWorksDW в колонке слева. Откроется база данных.
   
    ![Power BI: открытие AdventureWorksDW][5]

## <a name="2-create-a-report"></a>2) Создание отчета
Теперь вы готовы к использованию Power BI для анализа демонстрационных данных AdventureWorksDW. Для выполнения анализа в AdventureWorksDW предусмотрено представление с именем AggregateSales. Это представление содержит несколько основных метрик для анализа продаж компании.

1. Чтобы создать карту суммы продаж по почтовому индексу, разверните представление AggregateSales, щелкнув его в области полей справа. Выберите столбцы PostalCode и SalesAmount.
   
    ![Power BI: выбор AggregateSales][6]
   
    Power BI в автоматическом режиме распознает географические данные и наносит их на карту.
   
    ![Карта Power BI][7]
2. На этом этапе создается линейчатая диаграмма, на которой отображается сумма продаж в зависимости от доходов клиентов. Чтобы создать эту диаграмму, перейдите к развернутому представлению AggregateSales. Щелкните поле SalesAmount. Перетащите поле «Доход клиентов» влево и поместите его на оси.
   
    ![Power BI: выбор оси][8]
   
    Мы переместили линейчатую диаграмму влево.
   
    ![Гистограмма Power BI][9]
3. На этом этапе создается график, на котором отображены продажи по дате заказа. Чтобы создать эту диаграмму, перейдите к развернутому представлению AggregateSales. Щелкните SalesAmount и OrderDate. В столбце «Визуализации» щелкните значок «График» (это первый значок во второй строке под заголовком «Визуализации»).
   
    ![Power BI: выбор графика][10]
   
    Теперь у вас есть отчет, на котором отображены три разные визуализации.
   
    ![График Power BI][11]

Ход выполнения можно сохранить в любой момент, выбрав в меню **Файл** пункт **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы разобрались с образцом данных, узнайте, как [разрабатывать хранилища данных][develop], [загружать данные][load] и [переносить готовые решения][migrate]. Кроме того, рекомендуем ознакомиться с [веб-сайтом Power BI][Power BI website].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure portal]: https://portal.azure.com/
[Power BI website]: http://www.powerbi.com/

