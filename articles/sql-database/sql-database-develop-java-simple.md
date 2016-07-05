<properties
	pageTitle="Подключение к Базе данных SQL с помощью Java и драйвера JDBC в операционной системе Windows | Microsoft Azure"
	description="В статье представлен пример кода Java, который можно использовать для подключения к базе данных SQL Azure. В примере кода драйвер JDBC запускается на компьютере с клиентской версией Windows."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="lbosq"/>


# Подключение к Базе данных SQL с помощью Java и драйвера JDBC в операционной системе Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этой статье представлен пример кода Java, который можно использовать для подключения к Базе данных SQL Azure. Для использования примера кода Java требуется пакет средств разработки Java Development Kit (JDK) версии 1.8. Подключение к Базе данных SQL Azure осуществляется с помощью драйвера JDBC.

## Шаг 1. Настройка среды разработки

[Настройка среды разработки Java](https://msdn.microsoft.com/library/mt720658.aspx)

## Шаг 2. Создание базы данных SQL

Чтобы узнать, как создать базу данных, перейдите на страницу [Начало работы](sql-database-get-started.md).

## Шаг 3. Получение строки подключения

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] При использовании драйвера JTDS JDBC необходимо будет добавить ssl=require в URL-адрес строки подключения и задать для виртуальной машины Java параметр -Djsse.enableCBCProtection=false. Этот параметр виртуальной машины Java отключает исправление для уязвимости системы безопасности, поэтому убедитесь, что понимаете все связанные с ним риски, прежде чем задавать этот параметр.

## Шаг 4. Выполнение примера кода

* [Эксперимент по подключению к SQL с помощью Java](https://msdn.microsoft.com/library/mt720656.aspx)

## Дальнейшие действия

* Посетите [Центр разработчиков для Java](/develop/java/).
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md).
* Получите дополнительные сведения о драйвере [Microsoft JDBC Driver для SQL Server](https://msdn.microsoft.com/library/mt484311.aspx).

## Дополнительные ресурсы 

* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0622_2016-->