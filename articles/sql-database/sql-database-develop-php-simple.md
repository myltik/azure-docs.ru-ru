<properties
	pageTitle="Подключение к базе данных SQL с помощью приложения PHP в операционной системе Windows | Microsoft Azure"
	description="В этой статье представлен пример программы PHP, которая подключается к базе данных SQL Azure из клиентской ОС Windows, и показаны ссылки на программные компоненты, необходимые для клиента."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="meetb"/>


# Подключение к базе данных SQL с помощью приложения PHP в операционной системе Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этом разделе объясняется, как подключиться к базе данных SQL Azure из клиентского приложения PHP, которое запущено в ОС Windows.

## Шаг 1. Настройка среды разработки

[Настройка среды разработки PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## Шаг 2. Создание базы данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.


## Шаг 3. Получение сведений о подключении

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Шаг 4. Выполнение примера кода

* [Эксперимент по подключению к SQL с помощью PHP](https://msdn.microsoft.com/library/mt720665.aspx)
* [Отказоустойчивое подключение к SQL с помощью PHP](https://msdn.microsoft.com/library/mt720667.aspx)


## Дальнейшие действия

* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md).
* Получите дополнительные сведения о [драйвере Microsoft PHP для SQL Server](https://msdn.microsoft.com/library/dn865013.aspx).
* Дополнительные сведения об установке и использовании PHP см. в статье [Доступ к базам данных SQL Server с помощью PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## Дополнительные ресурсы 

* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0622_2016-->