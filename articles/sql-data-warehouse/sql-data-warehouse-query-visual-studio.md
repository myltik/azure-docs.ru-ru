---
title: "Запросы к хранилищу данных SQL Azure (Visual Studio) | Документация Майкрософт"
description: "Отправка запросов к хранилищу данных SQL с помощью Visual Studio."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: daace889-95e5-4826-b2fc-047eac9d6d95
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e97fefdf3cc13f2fd3e060da901e90ef5ef9a29


---
# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Запросы к хранилищу данных SQL Azure (Visual Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

В Visual Studio можно отправлять запросы к хранилищу данных SQL Azure за считанные минуты. В этом методе используется расширение SQL Server Data Tools (SSDT) в Visual Studio. 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством необходимы указанные ниже компоненты.

* Существующее хранилище данных SQL. Чтобы создать его, см. статью [Создание хранилища данных SQL Azure][Создание хранилища данных SQL Azure].
* Расширение SSDT для Visual Studio. Скорее всего, оно уже есть, если на вашем компьютере установлено приложение Visual Studio. Инструкции по установке и доступные варианты установки см. в статье [Установка Visual Studio 2015 и SSDT для хранилища данных SQL][Установка Visual Studio 2015 и SSDT для хранилища данных SQL].
* Полное имя сервера SQL Server. Чтобы найти его, см. статью [Подключение к хранилищу данных SQL Azure][Подключение к хранилищу данных SQL Azure].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Подключение к хранилищу данных SQL
1. Откройте Visual Studio 2013 или 2015.
2. Откройте обозреватель объектов SQL Server. Чтобы сделать это, выберите **Представление** > **Обозреватель объектов SQL Server**.
   
    ![Обозреватель объектов SQL Server][1]
3. Щелкните значок **Добавить SQL Server** .
   
    ![Добавить SQL Server][2]
4. Заполните поля в окне «Подключение к серверу».
   
    ![Подключение к серверу][3]
   
   * **Имя сервера**. Введите найденное **имя сервера** .
   * **Проверка подлинности**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
   * **Имя пользователя** и **Пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
   * Щелкните **Подключить**.
5. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.
   
    ![Обзор AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2) Запуск пробного запроса
Теперь, когда мы подключились к базе данных, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите пункт **Создать запрос**. Откроется окно нового запроса.
   
    ![Создать запрос][5]
3. Скопируйте следующий запрос TSQL в окно запроса.
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Выполните запрос. Для этого щелкните зеленую стрелку или воспользуйтесь сочетанием клавиш `CTRL`+`SHIFT`+`E`.
   
    ![Выполнение запроса][6]
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.
   
    ![Результаты запроса][7]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью PowerBI][визуализация данных с помощью PowerBI].

Дополнительные сведения о настройке в своей среде проверки подлинности Azure Active Directory, см. в статье [Аутентификация в хранилище данных SQL Azure][Аутентификация в хранилище данных SQL Azure].

<!--Arcticles-->
[Подключение к хранилищу данных SQL]: sql-data-warehouse-connect-overview.md
[Создание хранилища данных SQL Azure]: sql-data-warehouse-get-started-provision.md
[Установка Visual Studio и SSDT]: sql-data-warehouse-install-visual-studio.md
[Проверка подлинности в хранилище данных SQL Azure]: sql-data-warehouse-authentication.md
[визуализировать данные с помощью PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Портал Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png



<!--HONumber=Nov16_HO2-->


