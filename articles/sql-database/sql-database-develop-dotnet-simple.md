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
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/20/2016"
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

## Дополнительные ресурсы

Сведения о распространенных шаблонах архитектуры данных для мультитенантных приложений базы данных SaaS см. в статье [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->