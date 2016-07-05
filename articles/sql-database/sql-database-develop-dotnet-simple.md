<properties
	pageTitle="Подключение к базе данных SQL с помощью .NET (C#) | Microsoft Azure"
	description="Используйте приведенный в этом кратком руководстве пример кода, чтобы с помощью Базы данных SQL Azure разработать современное приложение на C# на основе мощной облачной реляционной базы данных."
	services="sql-database"
	documentationCenter=""
	authors="tobbox"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="tobiast"/>

# Подключение к базе данных SQL с помощью .NET (C#)

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## Шаг 1. Настройка среды разработки

[Настройка среды разработки ADO.NET](https://msdn.microsoft.com/library/mt718321.aspx)

## Шаг 2. Создание базы данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.

## Шаг 3. Получение строки подключения

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## Шаг 4. Выполнение примера кода

* [Подтверждение концепции подключения к SQL с использованием ADO.NET](https://msdn.microsoft.com/library/mt718320.aspx)
* [Отказоустойчивое подключение к SQL с помощью ADO.NET](https://msdn.microsoft.com/library/mt703195.aspx)

## Дальнейшие действия

* [Создание приложения ASP.NET MVC с проверкой подлинности и базой данных SQL и развертывание службы приложений Azure](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* [Сборка службы с использованием существующей базы данных SQL на внутреннем сервере мобильных служб .NET](../mobile-services/mobile-services-dotnet-backend-use-existing-sql-database.md)
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md).
* Получите дополнительные сведения о [драйвере Microsoft ADO.Net для SQL Server](https://msdn.microsoft.com/library/mt657768.aspx).

## Дополнительные ресурсы 

* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0622_2016-->