---
title: "Подключение к базе данных SQL с помощью приложения PHP в операционной системе Windows | Документация Майкрософт"
description: "В этой статье представлен пример программы PHP, которая подключается к базе данных SQL Azure из клиентской ОС Windows, и показаны ссылки на программные компоненты, необходимые для клиента."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: e1c3e7e0f6ca097e3ee41995defe5c1df666d39e


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Подключение к базе данных SQL с помощью приложения PHP в операционной системе Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этом разделе объясняется, как подключиться к базе данных SQL Azure из клиентского приложения PHP, которое запущено в ОС Windows.

## <a name="step-1--configure-development-environment"></a>Шаг 1. Настройка среды разработки
[Настройка среды разработки PHP](https://docs.microsoft.com/sql/connect/php/step-1-configure-development-environment-for-php-development/)

## <a name="step-2-create-a-sql-database"></a>Шаг 2. Создание базы данных SQL
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Шаг 3. Получение сведений о подключении
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Шаг 4. Выполнение примера кода
* [Эксперимент по подключению к SQL с помощью PHP](https://docs.microsoft.com/sql/connect/php/step-3-proof-of-concept-connecting-to-sql-using-php/)
* [Отказоустойчивое подключение к SQL с помощью PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php/)

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о [драйвере Microsoft PHP для SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server/)
* Дополнительные сведения об установке и использовании PHP см. в статье [Accessing SQL Server Databases from PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx) (Доступ к базам данных SQL Server из приложения PHP).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


