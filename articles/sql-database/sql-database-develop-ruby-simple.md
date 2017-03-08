---
title: "Подключение к базе данных SQL с помощью Ruby | Документация Майкрософт"
description: "Позвольте примеру кода Ruby, который вы можете выполнить, подключиться к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 02/03/2017
ms.author: andrela
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: cbd13711911b67ace7ef43676b4c52aa93744bcb
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-ruby"></a>Подключение к базе данных SQL с помощью Ruby
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этом разделе описывается подключение и запрос к базе данных SQL Azure с использованием Ruby. Приведенный пример можно выполнить в операционной системе Windows, Ubuntu Linux или Mac.

## <a name="step-1-configure-development-environment"></a>Шаг 1. Настройка среды разработки
[Предварительные требования для использования драйвера Ruby TinyTDS для SQL Server](https://docs.microsoft.com/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/)

## <a name="step-2-create-a-sql-database"></a>Шаг 2. Создание базы данных SQL
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Шаг 3. Получение сведений о подключении
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Шаг 4. Выполнение примера кода
[Эксперимент по подключению к SQL с помощью Ruby](https://docs.microsoft.com/sql/connect/ruby/step-3-proof-of-concept-connecting-to-sql-using-ruby/)

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о [драйвере Microsoft Ruby для SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)


