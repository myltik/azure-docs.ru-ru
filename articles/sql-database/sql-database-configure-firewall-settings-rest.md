---
title: "Настройка правил брандмауэра уровня сервера Базы данных SQL Azure с помощью REST API | Документация Майкрософт"
description: "Сведения о настройке брандмауэра для IP-адресов, через которые осуществляется доступ к базам данных SQL Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: cd5d47aa012b21a283e3d6482fe0afddcfa14033


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью REST API
> [!div class="op_single_selector"]
> * [Обзор](sql-database-firewall-configure.md)
> * [Портал Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Для подключения к серверам и базам данных в Базе данных SQL Microsoft Azure используются правила брандмауэра. На сервере Базы данных SQL Azure для главной или пользовательской базы данных можно определить значения параметров брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [!IMPORTANT]
> Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в разделе [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Если вы создаете подключения внутри облака Azure, вам, возможно, придется открыть дополнительные TCP-порты. Дополнительные сведения см. в разделе **Версия 12 базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md).
> 
> 

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Управление правилами брандмауэра на уровне сервера с помощью REST API
1. Запрос на управление правилами брандмауэра с помощью REST API должен пройти проверку подлинности. Дополнительные сведения см. в [руководстве разработчика по авторизации с помощью API Azure Resource Manager](../resource-manager-api-authentication.md).
2. Правила на уровне сервера можно создавать, обновлять или удалять с помощью REST API
   
    Чтобы создать или обновить правило брандмауэра на уровне сервера, выполните метод PUT следующим образом:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    Текст запроса
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    Для удаления существующего правила брандмауэра на уровне сервера выполните метод DELETE следующим образом:

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Управление правилами брандмауэра с помощью REST API
* [Создание и изменение правила брандмауэра](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Удаление правила брандмауэра](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Получение правила брандмауэра](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Получение списка всех правил брандмауэра](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>Дальнейшие действия
Практическое руководство по использованию службы Transact-SQL для создания правил брандмауэра уровня сервера и уровня базы данных см. в статье [Настройка правил брандмауэра уровня сервера базы данных SQL Azure и уровня базы данных SQL Azure с помощью T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Практические руководства по созданию правил брандмауэра уровня сервера с помощью других методов см. в следующих разделах. 

* [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md)
* [Настройка правил брандмауэра уровня сервера базы данных SQL с помощью PowerShell](sql-database-configure-firewall-settings-powershell.md)

Учебник по созданию базы данных SQL доступен в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md).
Дополнительные сведения о подключении к базе данных SQL Azure из приложений с открытым кодом или приложений сторонних производителей см. в статье [Библиотеки подключений для базы данных SQL и SQL Server](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Общие сведения о навигации по базам данных см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Защита базы данных](sql-database-security.md)
* [Центр обеспечения безопасности для ядра СУБД SQL Server и базы данных Azure SQL](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->





<!--HONumber=Dec16_HO2-->


