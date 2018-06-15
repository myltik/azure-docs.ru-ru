---
title: Подключение к хранилищу данных SQL Azure — VSTS | Документация Майкрософт
description: Отправка запросов к хранилищу данных SQL с помощью Visual Studio.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 1bd3516661a73e7a6d9aaceed589a55cd650453e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791262"
---
# <a name="connect-to-sql-data-warehouse-with-visual-studio-and-ssdt"></a>Подключение к хранилищу данных SQL с помощью Visual Studio и SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

В Visual Studio можно отправлять запросы к хранилищу данных SQL Azure за считанные минуты. В этом методе используется расширение SQL Server Data Tools (SSDT) в Visual Studio. 

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством необходимы указанные ниже компоненты.

* Существующее хранилище данных SQL. Сведения о его создании см. в статье [Создание хранилища данных SQL Azure][Create a SQL Data Warehouse].
* Расширение SSDT для Visual Studio. Скорее всего, оно уже есть, если на вашем компьютере установлено приложение Visual Studio. Инструкции по установке и доступные варианты установки см. в статье [Установка Visual Studio 2015 и SSDT для хранилища данных SQL][Installing Visual Studio and SSDT].
* Полное имя сервера SQL Server. Эти сведения можно узнать в статье [Подключение к хранилищу данных SQL Azure][Connect to SQL Data Warehouse].

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

## <a name="2-run-a-sample-query"></a>2. Запуск пробного запроса
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

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью PowerBI][visualizing the data with PowerBI].

Сведения о том, как настроить проверку подлинности Azure Active Directory в своей среде, см. в статье [Проверка подлинности в хранилище данных SQL Azure][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

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
