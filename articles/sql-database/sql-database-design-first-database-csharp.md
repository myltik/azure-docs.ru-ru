---
title: "Проектирование первой базы данных SQL Azure на языке C# | Документация Майкрософт"
description: "Узнайте, как спроектировать свою первую базу данных SQL Azure и подключиться к ней с помощью программы на языке C#, используя ADO.NET."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/20/2017
ms.author: genemi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 069656b495929d66f6a47b38141f56b6bf0ac92f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017

---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Проектирование базы данных SQL Azure и подключение к ней с помощью C# и ADO.NET

База данных SQL Azure — это реляционная база данных как служба (DBaaS) в Microsoft Cloud (Azure). Из этого руководства вы узнаете, как с помощью портала Azure и ADO.NET: 

> [!div class="checklist"]
> * создать базу данных на портале Azure;
> * настроить правило брандмауэра на уровне сервера на портале Azure;
> * подключиться к базе данных с помощью ADO.NET;
> * создать таблицы с помощью ADO.NET;
> * вставить данные с помощью ADO.NET; 
> * выполнить запрос этих данных с помощью ADO.NET.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Дальнейшие действия

Вы можете перейти к следующим действиям:

- [Использование LINQ в программе на языке C# для SQL-запросов](https://msdn.microsoft.com/library/bb425822.aspx)
- [Перенос базы данных SQL Server в базу данных SQL Azure](sql-database-migrate-your-sql-server-database.md)

