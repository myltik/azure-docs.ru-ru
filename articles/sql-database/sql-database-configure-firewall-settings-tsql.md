---
title: "T-SQL: правила брандмауэра базы данных SQL Azure | Документация Майкрософт"
description: "Узнайте, как настроить правила брандмауэра на уровне сервера и базы данных для IP-адресов, по которым получается доступ к базам данных SQL Azure, с использованием Transact-SQL."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/07/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 84efba61fbb0248edef3036de702e3a1aa8ae903
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Настройка правил брандмауэра уровня сервера базы данных SQL Azure и уровня базы данных SQL Azure с помощью T-SQL

Для подключения к серверам и базам данных в Базе данных SQL Microsoft Azure используются правила брандмауэра. На сервере Базы данных SQL Azure для главной или пользовательской базы данных можно определить значения параметров брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [!IMPORTANT]
> Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в разделе [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Если вы создаете подключения внутри облака Azure, вам, возможно, придется открыть дополнительные TCP-порты. Дополнительные сведения см. в разделе **Версия 12 базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md).
> 
> 

## <a name="server-level-firewall-rules"></a>Правила брандмауэра на уровне сервера
Правило брандмауэра уровня сервера с помощью Transact-SQL может создавать только администратор сервера Azure SQL Server или администратор Azure Active Directory.

1. Откройте окно запроса и подключитесь к главной виртуальной базе данных с помощью SQL Server Management Studio.
2. В окне запроса можно создавать, обновлять или удалять правила брандмауэра на уровне сервера.
3. Для создания или обновления правил брандмауэра уровня сервера выполните хранимую процедуру `sp_set_firewall_rule` . В следующем примере показано, как задать диапазон IP-адресов на сервере Contoso.<br/>Для начала узнайте, какие правила уже существуют.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    Добавьте правило брандмауэра.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    Для удаления правил брандмауэра на уровне сервера выполните хранимую процедуру sp_delete_firewall_rule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   Дополнительные сведения об этих хранимых процедурах см. на страницах [sp_set_firewall_rule (база данных SQL Azure)](https://msdn.microsoft.com/library/dn270017.aspx) и [sp_delete_firewall_rule (база данных SQL Azure)](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Правила брандмауэра на уровне базы данных
Правило брандмауэра уровня базы данных может создавать только пользователь базы данных с разрешением **УПРАВЛЕНИЕ** для базы данных (например, владелец базы данных).

1. После создания правила брандмауэра уровня сервера для своего IP-адреса откройте окно запроса на портале Azure или с помощью SQL Server Management Studio.
2. Подключение к базе данных, для которой нужно создать правило брандмауэра на уровне базы данных.
   
    Для создания нового или обновления существующего правила брандмауэра уровня базы данных выполните хранимую процедуру `sp_set_database_firewall_rule` . В следующем примере показано, как создать новое правило с именем ContosoFirewallRule.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    Для удаления существующего правила брандмауэра уровня базы данных выполните хранимую процедуру `sp_delete_database_firewall_rule` . В следующем примере показано, как удалить правило с именем ContosoFirewallRule.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Дополнительные сведения об этих хранимых процедурах см. на страницах [sp_set_database_firewall_rule (база данных SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx) и [sp_delete_database_firewall_rule (база данных SQL Azure)](https://msdn.microsoft.com/library/dn270030.aspx).

> [!NOTE]
> Сведения об использовании брандмауэров уровня базы данных см. в статье [Руководство по базам данных SQL: аутентификация, доступ и правила брандмауэра уровня базы данных в SQL Server](sql-database-control-access-sql-authentication-get-started.md).
>


## <a name="next-steps"></a>Дальнейшие действия
Сведения о создании правил брандмауэра уровня сервера с помощью других методов см. в следующих статьях: 

* [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью PowerShell](sql-database-configure-firewall-settings.md)
* [Настройка правил брандмауэра уровня сервера базы данных SQL с помощью PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью REST API](sql-database-configure-firewall-settings-rest.md)

Руководство по созданию базы данных см. в статье [Краткое руководство. Начало работы с базой данных SQL Azure](sql-database-get-started.md).
Дополнительные сведения о подключении к базе данных SQL Azure из приложений с открытым кодом или приложений сторонних производителей см. в статье [Библиотеки подключений для базы данных SQL и SQL Server](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Общие сведения о навигации по базам данных см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Защита базы данных](sql-database-security-overview.md)
* [Центр обеспечения безопасности для ядра СУБД SQL Server и базы данных Azure SQL](https://msdn.microsoft.com/library/bb510589)


