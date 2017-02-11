---
title: "Подключение к хранилищу данных SQL Azure — SSMS | Документация Майкрософт"
description: "Подключайтесь к хранилищу данных SQL Azure и создавайте запросы к нему с помощью SQL Server Management Studio (SSMS)."
services: sql-data-warehouse
documentationcenter: 
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 299e50b3-e68a-471c-8aee-b0b9874781bd
ms.service: sql-data-warehouse
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/31/2016
ms.author: elbutter;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 77474214c6fafe7f591030d30f6a46c66fbc5c09
ms.openlocfilehash: 30ac3558534e96b63d78f9c66d42d11b7c1a3c75


---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Подключение к хранилищу данных SQL Azure с помощью SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Подключайтесь к хранилищу данных SQL Azure и создавайте запросы к нему с помощью SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством необходимы указанные ниже компоненты.

* Существующее хранилище данных SQL. Чтобы создать его, см. статью [Создание хранилища данных SQL Azure][Создание хранилища данных SQL Azure].
* Установленный SQL Server Management Studio (SSMS). [Установите SSMS][установка SSMS] бесплатно, если вы еще этого не сделали.
* Полное имя сервера SQL Server. Чтобы найти его, см. статью [Подключение к хранилищу данных SQL Azure][Подключение к хранилищу данных SQL Azure].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Подключение к хранилищу данных SQL
1. Откройте среду SSMS.
2. Откройте обозреватель объектов. Чтобы сделать это, выберите **Файл** > **Подключить к обозревателю объектов**.
   
    ![Обозреватель объектов SQL Server][1]
3. Заполните поля в окне «Подключение к серверу».
   
    ![Подключение к серверу][2]
   
   * **Имя сервера**. Введите найденное **имя сервера** .
   * **Проверка подлинности**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
   * **Имя пользователя** и **Пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
   * Щелкните **Подключить**.
4. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.
   
    ![Обзор AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2) Запуск пробного запроса
Теперь, когда мы подключились к базе данных, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите пункт **Создать запрос**. Откроется окно нового запроса.
   
    ![Создать запрос][4]
3. Скопируйте следующий запрос TSQL в окно запроса.
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Выполните запрос. Для этого щелкните `Execute` или воспользуйтесь клавишами `F5`.
   
    ![Выполнение запроса][5]
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.
   
    ![Результаты запроса][6]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью PowerBI][визуализировать данные с помощью PowerBI].

Дополнительные сведения о настройке в своей среде проверки подлинности Azure Active Directory, см. в статье [Аутентификация в хранилище данных SQL Azure][Аутентификация в хранилище данных SQL Azure].

<!--Arcticles-->
[Подключение к хранилищу данных SQL Azure]: sql-data-warehouse-connect-overview.md
[Создание хранилища данных SQL Azure]: sql-data-warehouse-get-started-provision.md
[Аутентификация в хранилище данных SQL Azure]: sql-data-warehouse-authentication.md
[визуализировать данные с помощью PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[портал Azure]: https://portal.azure.com
[установка SSMS]: https://msdn.microsoft.com/en-US/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png



<!--HONumber=Nov16_HO3-->


