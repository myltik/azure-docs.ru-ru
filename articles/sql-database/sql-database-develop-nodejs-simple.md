---
title: "Подключение к базе данных SQL с помощью Node.js | Документация Майкрософт"
description: "В этой статье представлен пример кода Node.js, который можно использовать для подключения к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: 6c3f6bc00d147bc498a859560ffe4719ecf888bf


---
# <a name="connect-to-sql-database-by-using-nodejs"></a>Подключение к базе данных SQL с помощью Node.js
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этом разделе описывается подключение и запрос к базе данных SQL Azure с использованием Node.js. Приведенный пример можно выполнить в операционной системе Windows, Ubuntu Linux или Mac.

## <a name="step-1-configure-development-environment"></a>Шаг 1. Настройка среды разработки
[Предварительные требования для использования драйвера Tedious Node.js для SQL Server](https://docs.microsoft.com/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/)

## <a name="step-2-create-a-sql-database"></a>Шаг 2. Создание базы данных SQL
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Шаг 3. Получение сведений о подключении
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Шаг 4. Выполнение примера кода
[Эксперимент по подключению к SQL с использованием Node.js](https://docs.microsoft.com/sql/connect/node-js/step-3-proof-of-concept-connecting-to-sql-using-node-js/)

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о [драйвере Microsoft Node.js для SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


