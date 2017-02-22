---
title: "Подключение к базе данных SQL с помощью запроса C# | Документация Майкрософт"
description: "Написание программы на C# для отправки запроса к базе данных SQL и подключения к ней. Сведения об IP адресах, строках подключения, безопасном входе и бесплатной версии Visual Studio."
services: sql-database
keywords: "запрос c# к базе данных, запрос c#, подключение к базе данных, SQL C#"
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 317193c178c4da283de7d61204aa7b824170d72a


---
# <a name="connect-to-a-sql-database-with-visual-studio"></a>Подключение к базе данных SQL с помощью Visual Studio

Узнайте, как подключиться к базе данных SQL Azure в Visual Studio. 

## <a name="prerequisites"></a>Предварительные требования
Обязательные требования для подключения к базе данных SQL с помощью Visual Studio: 

* База данных SQL для подключения. В нашем примере используется пример базы данных **AdventureWorks** . Получить пример базы данных AdventureWorks можно [здесь](sql-database-get-started.md).
* Visual Studio 2013 с обновлением 4 или более поздняя версия. Майкрософт предлагает *бесплатную*версию Visual Studio Community.
  
  * [Загрузка Visual Studio Community](http://www.visualstudio.com/products/visual-studio-community-vs)
  * [Дополнительные варианты бесплатного использования Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)

## <a name="open-visual-studio-from-the-azure-portal"></a>Открытие Visual Studio с портала Azure
1. Войдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **Другие службы** > **Базы данных SQL**.
3. Откройте колонку базы данных **AdventureWorks** и щелкните базу данных *AdventureWorks* .
4. В верхней части колонки базы данных щелкните **Инструменты** .
   
    ![Новый запрос Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query/tools.png)
5. Щелкните **Открыть в Visual Studio** (если вам нужно установить Visual Studio, щелкните ссылку для скачивания):
   
    ![Новый запрос Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)
6. В Visual Studio откроется окно **подключения к серверу** с предустановленными параметрами для подключения к серверу и базе данных, выбранным на портале.  (Щелкните **Параметры**, чтобы проверить, что вы подключаетесь к нужной базе данных). Введите пароль администратора сервера и нажмите кнопку **Подключиться**.

    ![Новый запрос Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


1. Если правило брандмауэра не настроено для IP-адреса компьютера, вы получите сообщение *Не удается подключиться*. См. статью [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).
2. После успешного подключения к базе данных откроется окно **обозревателя объектов SQL Server** .
   
    ![Новый запрос Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)

## <a name="run-a-sample-query"></a>Запуск пробного запроса
Подключившись к базе данных, можно приступать к выполнению простого запроса.

1. Щелкните правой кнопкой мыши базу данных и выберите **Создать запрос**.
   
    ![Новый запрос Подключение к базе данных SQL с помощью SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)
2. В окне запроса скопируйте и вставьте следующий код.
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
3. Чтобы запустить запрос, нажмите кнопку **Выполнить** .
   
    ![Успешно. Подключение к серверу базы данных SQL с помощью Visual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Дальнейшие действия
* Открытие баз данных SQL в Visual Studio осуществляется с помощью SQL Server Data Tools. Дополнительные сведения см. в статье [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686.aspx).
* Чтобы подключиться к базе данных SQL программно, см. статью [Подключение к базе данных SQL с помощью .NET (C#)](sql-database-develop-dotnet-simple.md).




<!--HONumber=Feb17_HO3-->


