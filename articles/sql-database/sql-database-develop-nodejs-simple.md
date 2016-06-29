<properties
	pageTitle="Подключение к базе данных SQL с помощью Node.js | Microsoft Azure"
	description="В этой статье представлен пример кода Node.js, который можно использовать для подключения к базе данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="meetb"/>

# Подключение к базе данных SQL с помощью Node.js

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этом разделе описывается подключение и запрос к базе данных SQL Azure с использованием Node.js. Приведенный пример можно выполнить в операционной системе Windows, Ubuntu Linux или Mac.

## Шаг 1. Настройка среды разработки

[Предварительные требования для использования драйвера Tedious Node.js для SQL Server](https://msdn.microsoft.com/library/mt652094.aspx)

## Шаг 2. Создание базы данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.

## Шаг 3. Получение сведений о подключении

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Шаг 4. Выполнение примера кода

[Эксперимент по подключению к SQL с использованием Node.js](https://msdn.microsoft.com/library/mt715784.aspx)

## Дополнительные ресурсы

Сведения о распространенных шаблонах архитектуры данных для мультитенантных приложений базы данных SaaS см. в статье [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->