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
ms.date: 02/03/2017
ms.author: tobiast
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: cc756c662b97c64ab30a7d5bf2cc325f9e74d905
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-net-c"></a>Подключение к базе данных SQL с помощью .NET (C#)
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## <a name="step-1--configure-development-environment"></a>Шаг 1. Настройка среды разработки
[Настройка среды разработки ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-1-configure-development-environment-for-ado-net-development/)

## <a name="step-2-create-a-sql-database"></a>Шаг 2. Создание базы данных SQL
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.  

## <a name="step-3--get-connection-string"></a>Шаг 3. Получение строки подключения
[!INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Шаг 4. Выполнение примера кода
* [Подтверждение концепции подключения к SQL с использованием ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-3-proof-of-concept-connecting-to-sql-using-ado-net/)
* [Отказоустойчивое подключение к SQL с помощью ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net/)

## <a name="next-steps"></a>Дальнейшие действия
* [Создание приложения ASP.NET MVC с аутентификацией и базой данных SQL и его развертывание в службе приложений Azure](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о [драйвере Microsoft ADO.Net для SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server/)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)


