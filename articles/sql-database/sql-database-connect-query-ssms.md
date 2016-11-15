---
title: "Подключение к базе данных SQL с помощью SQL Server Management Studio | Документация Майкрософт"
description: "Узнайте, как подключиться к базе данных SQL в Azure с помощью SQL Server Management Studio (SSMS). Затем выполните пробный запрос с помощью Transact-SQL (T-SQL)."
metacanonical: 
keywords: "подключение к базе данных sql, sql server management studio"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0eb25eb76c6c6c2446ac0b2b07c65975c3719db0


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-tsql-query"></a>Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

Из этой статьи вы узнаете, как подключиться к базе данных SQL Azure с помощью SQL Server Management Studio (SSMS). После успешного подключения нужно запустить простой запрос Transact-SQL (T-SQL), чтобы проверить соединение с базой данных.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[!INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

## <a name="run-sample-queries"></a>Запуск образцов запросов
Установив подключение к серверу, можно подключиться к базе данных и выполнить образец запроса. Если вы новичок и еще не знаете, как писать запросы, см. статью [Учебник. Составление инструкций Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx).

1. В **обозревателе объектов** перейдите к базе данных на сервере, например к примеру базы данных **AdventureWorks**.
2. Щелкните правой кнопкой мыши базу данных и выберите **Создать запрос**.
   
    ![Новый запрос Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)
3. В окне запроса скопируйте и вставьте следующий код.
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
4. Нажмите кнопку **Выполнить** .
   
    ![Успешно. Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## <a name="next-steps"></a>Дальнейшие действия
Инструкции T-SQL можно использовать для создания баз данных в Azure и управления ими таким же образом, как и в SQL Server. Если вы знаете, как использовать T-SQL в SQL Server, ознакомьтесь с отличиями, приведенными в статье [Сведения об инструкциях Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md) .

Если вы не знакомы с T-SQL, см. [руководство по написанию инструкций Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) и [справочник по Transact-SQL (ядро СУБД)](https://msdn.microsoft.com/library/bb510741.aspx).

Сведения о том, как создавать пользователей и администраторов базы данных, см. в статье [Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure](sql-database-get-started-security.md).

Дополнительные сведения о решении SSMS см. в статье [Использование среды SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).




<!--HONumber=Nov16_HO2-->


