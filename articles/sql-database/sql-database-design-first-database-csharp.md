---
title: Проектирование первой базы данных SQL Azure на языке C# | Документация Майкрософт
description: Узнайте, как спроектировать свою первую базу данных SQL Azure и подключиться к ней с помощью программы на языке C#, используя ADO.NET.
services: sql-database
author: MightyPen
manager: craigg-msft
ms.reviewer: CarlRabeler
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.topic: tutorial
ms.date: 06/07/2018
ms.openlocfilehash: 4e2238968d29bc0081a472c9c05662cb7813d866
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850667"
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Проектирование базы данных SQL Azure и подключение к ней с помощью C# и ADO.NET

База данных SQL Azure — это реляционная база данных как услуга (DBaaS) в Microsoft Cloud (Azure). Из этого руководства вы узнаете, как с помощью портала Azure и ADO.NET с Visual Studio выполнить следующие действия: 

> [!div class="checklist"]
> * создать базу данных на портале Azure;
> * настроить правило брандмауэра на уровне сервера на портале Azure;
> * подключиться к базе данных с помощью ADO.NET и Visual Studio;
> * создать таблицы с помощью ADO.NET;
> * вставить, обновить и удалить данные с помощью ADO.NET; 
> * выполнить запрос данных с помощью ADO.NET.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Убедитесь, что установлен [Visual Studio Community 2017, Visual Studio Professional 2017 или Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали об основных задачах базы данных, таких как создание базы данных и таблиц, загрузка и запрос данных, а также восстановление базы данных до предшествующей точки во времени. Вы научились выполнять следующие задачи:
> [!div class="checklist"]
> * Создание базы данных
> * Настройка правила брандмауэра.
> * подключаться к базе данных с помощью [Visual Studio и C#](sql-database-connect-query-dotnet-visual-studio.md);
> * создание таблиц.
> * вставлять, обновлять и удалять данные;
> * Запрос данных

Перейдите к следующему руководству, чтобы узнать о переносе данных.

> [!div class="nextstepaction"]
> [Перенос базы данных SQL Server в базу данных SQL Azure](sql-database-migrate-your-sql-server-database.md)

