---
title: "Руководство по базам данных SQL: обеспечение безопасности"
description: "Узнайте, как создавать учетные записи пользователей для доступа к базе данных и управления ею."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: auth and access
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: e846654b659c1240451e3ac26973fbfce206936d


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure
> [!div class="op_single_selector"]
> * [Учебник по началу работы](sql-database-get-started-security.md)
> * [Предоставление доступа](sql-database-manage-logins.md)
> 
> 

В этом учебнике вы узнаете, как с помощью SQL Server Management Studio (SSMS) выполнять такие действия:

* входить в базу данных SQL с использованием учетных данных субъекта на уровне сервера;
* создавать учетную запись пользователя базы данных SQL;
* предоставлять новому пользователю базы данных [разрешения db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0);
* подключаться к базе данных SQL с помощью учетной записи, которая не является субъектом уровня сервера.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы завершили работу с этим руководством по базам данных SQL, создав учетную запись пользователя и предоставив разрешения dbo учетной записи пользователя, узнайте, как [защитить базу данных SQL](sql-database-manage-logins.md).




<!--HONumber=Dec16_HO1-->


