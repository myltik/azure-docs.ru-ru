---
title: "Подключение к базе данных SQL с помощью SQL Server Management Studio | Документация Майкрософт"
description: "Узнайте, как подключиться к базе данных SQL в Azure с помощью SQL Server Management Studio (SSMS). Затем выполните пробный запрос с помощью Transact-SQL (T-SQL)."
metacanonical: 
keywords: "подключение к базе данных sql, sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2a85b3dc1078bad9e5e2fc0ce0bec7e994b29150
ms.openlocfilehash: a6b147521525fad343376db0454f786a77b55c42


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-t-sql-query"></a>Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 

Из этой статьи вы узнаете, как подключиться к базе данных SQL Azure с помощью SQL Server Management Studio (SSMS). После успешного подключения нужно запустить простой запрос Transact-SQL (T-SQL), чтобы проверить соединение с базой данных.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

1. Сведения о том, как скачать и установить последнюю версию SSMS, см. в статье [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Когда новая версия становится доступной для скачивания, последняя версия SSMS выдает соответствующий запрос. Это позволяет всегда пользоваться обновленным ПО.

2. После установки введите в поле поиска Windows текст **Microsoft SQL Server Management Studio** и нажмите клавишу **ВВОД**, чтобы запустить приложение.

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. В диалоговом окне подключения к серверу введите данные, необходимые для подключения к серверу SQL Server с использованием проверки подлинности SQL Server.

    ![Подключение к серверу](./media/sql-database-get-started/connect-to-server.png)
4. Щелкните **Подключить**.

    ![Подключение к серверу установлено](./media/sql-database-get-started/connected-to-server.png)
5. В обозревателе объектов разверните **Базы данных**, затем разверните любую базу данных, чтобы просмотреть находящиеся в ней объекты.

    ![Объекты новой базы данных в SSMS](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
6. Щелкните правой кнопкой мыши эту базу данных и выберите пункт **Создать запрос**.

    ![Запрос к новому примеру базы данных в SSMS](./media/sql-database-get-started/new-sample-db-query-ssms.png)
7. В окне запроса введите следующее:

   ```select * from sys.objects```
   
8.  На панели инструментов нажмите кнопку **Выполнить**, чтобы получить список всех системных объектов в примере базы данных.

    ![Запрос на получение системных объектов из нового примера базы данных в SSMS](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

> [!Tip]
> Подробное руководство см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md).    
>

## <a name="next-steps"></a>Дальнейшие действия

- Инструкции T-SQL можно использовать для создания баз данных в Azure и управления ими таким же образом, как и в SQL Server. Если вы знаете, как использовать T-SQL в SQL Server, ознакомьтесь с отличиями, приведенными в статье [Сведения об инструкциях Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md) .
- Если вы не знакомы с T-SQL, см. [руководство по написанию инструкций Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) и [справочник по Transact-SQL (ядро СУБД)](https://msdn.microsoft.com/library/bb510741.aspx).
- Сведения о том, как создавать пользователей и администраторов базы данных, см. в статье [Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure](sql-database-control-access-sql-authentication-get-started.md).
- Дополнительные сведения о решении SSMS см. в статье [Использование среды SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).




<!--HONumber=Jan17_HO3-->


