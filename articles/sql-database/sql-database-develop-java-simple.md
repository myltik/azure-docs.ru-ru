---
title: "Подключение к базе данных SQL с помощью Java и драйвера JDBC в операционной системе Windows | Документация Майкрософт"
description: "В статье представлен пример кода Java, который можно использовать для подключения к базе данных SQL Azure. В примере кода драйвер JDBC запускается на компьютере с клиентской версией Windows."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: genemi
ms.assetid: 08fc49b1-cd48-4dcc-a293-ff22a4d2d62c
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: aafc89ca958e4bdc2ba52bf1a7d379e3a4cddf1c
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Подключение к Базе данных SQL с помощью Java и драйвера JDBC в операционной системе Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этой статье представлен пример кода Java, который можно использовать для подключения к Базе данных SQL Azure. Для использования примера кода Java требуется пакет средств разработки Java Development Kit (JDK) версии 1.8. Подключение к Базе данных SQL Azure осуществляется с помощью драйвера JDBC.

## <a name="step-1--configure-development-environment"></a>Шаг 1. Настройка среды разработки
[Настройка среды разработки Java](https://docs.microsoft.com/sql/connect/jdbc/step-1-configure-development-environment-for-java-development/)

## <a name="step-2-create-a-sql-database"></a>Шаг 2. Создание базы данных SQL
Чтобы узнать, как создать базу данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  

## <a name="step-3-get-connection-string"></a>Шаг 3. Получение строки подключения
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> При использовании драйвера JTDS JDBC необходимо будет добавить ssl=require в URL-адрес строки подключения и задать для виртуальной машины Java параметр -Djsse.enableCBCProtection=false. Этот параметр виртуальной машины Java отключает исправление для уязвимости системы безопасности, поэтому убедитесь, что понимаете все связанные с ним риски, прежде чем задавать этот параметр.
> 
> 

## <a name="step-4-run-sample-code"></a>Шаг 4. Выполнение примера кода
* [Эксперимент по подключению к SQL с помощью Java](https://docs.microsoft.com/sql/connect/jdbc/step-3-proof-of-concept-connecting-to-sql-using-java/)

## <a name="next-steps"></a>Дальнейшие действия
* Посетите [Центр разработчиков для Java](/develop/java/).
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о драйвере [Microsoft JDBC Driver для SQL Server](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)


