---
title: "Подключение к базе данных SQL с помощью .NET (C#) | Документация Майкрософт"
description: "Используйте приведенный в этом кратком руководстве пример кода, чтобы с помощью Базы данных SQL Azure разработать современное приложение на C# на основе мощной облачной реляционной базы данных."
services: sql-database
documentationcenter: 
author: tobbox
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/16/2016
ms.author: tobiast
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85e49c3c7f23f90942ca2f9382626369cce7140e


---
# <a name="connect-to-sql-database-by-using-net-c"></a>Подключение к базе данных SQL с помощью .NET (C#)
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## <a name="step-1--configure-development-environment"></a>Шаг 1. Настройка среды разработки
[Настройка среды разработки ADO.NET](https://msdn.microsoft.com/library/mt718321.aspx)

## <a name="step-2-create-a-sql-database"></a>Шаг 2. Создание базы данных SQL
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.  

## <a name="step-3--get-connection-string"></a>Шаг 3. Получение строки подключения
[!INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Шаг 4. Выполнение примера кода
* [Подтверждение концепции подключения к SQL с использованием ADO.NET](https://msdn.microsoft.com/library/mt718320.aspx)
* [Отказоустойчивое подключение к SQL с помощью ADO.NET](https://msdn.microsoft.com/library/mt703195.aspx)

## <a name="next-steps"></a>Дальнейшие действия
* [Создание приложения ASP.NET MVC с аутентификацией и базой данных SQL и его развертывание в службе приложений Azure](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о [драйвере Microsoft ADO.Net для SQL Server](https://msdn.microsoft.com/library/mt657768.aspx)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


