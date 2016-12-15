---
title: "Настройка правил брандмауэра уровня сервера Базы данных SQL с помощью PowerShell | Документация Майкрософт"
description: "Сведения о настройке брандмауэра для IP-адресов, через которые осуществляется доступ к базам данных SQL Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
ms.service: sql-database
ms.custom: auth and access
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 890ca8d9b7a3a76c6584f9d06cc8bdf3b36f87c0


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Настройка правил брандмауэра уровня сервера базы данных SQL с помощью PowerShell
> [!div class="op_single_selector"]
> * [Обзор](sql-database-firewall-configure.md)
> * [Портал Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Для подключения к серверам и базам данных в Базе данных SQL Azure используются правила брандмауэра. На сервере Базы данных SQL для базы данных master или пользовательской базы данных можно определить параметры брандмауэра уровня сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [!IMPORTANT]
> Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в разделе [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Если вы создаете подключения внутри облака Azure, вам, возможно, придется открыть дополнительные TCP-порты. Дополнительные сведения см. в разделе "Версия 12 Базы данных SQL: внешняя и внутренняя программа" статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md).
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Создание правил брандмауэра для сервера
Правила брандмауэра уровня сервера можно создавать, обновлять и удалять с помощью Azure PowerShell.

Чтобы создать правило брандмауэра на уровне сервера, выполните командлет [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). В следующем примере показано, как задать диапазон IP-адресов на сервере Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

Чтобы изменить новое правило брандмауэра на уровне сервера, выполните командлет [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx). В следующем примере показано, как изменить диапазон допустимых IP-адресов для правила с именем ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –FirewallRuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Чтобы удалить новое правило брандмауэра на уровне сервера, выполните командлет [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx). В следующем примере показано, как удалить правило с именем ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –FirewallRuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Управление правилами брандмауэра с помощью PowerShell
Для управления правилами брандмауэра можно также использовать PowerShell. Дополнительные сведения см. в следующих статьях:

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>Дальнейшие действия
Информацию о том, как использовать Transact-SQL для создания правил брандмауэра уровня сервера и уровня базы данных, см. в разделе [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью TSQL](sql-database-configure-firewall-settings-tsql.md).

Информацию о том, как создавать правила брандмауэра уровня сервера с помощью других методов, см. в следующих разделах.

* [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md)
* [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью REST API](sql-database-configure-firewall-settings-rest.md)

Учебник по созданию базы данных SQL доступен в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md).
Дополнительные сведения о подключении к базе данных SQL Azure из приложений с открытым кодом или приложений сторонних производителей см. в статье [Библиотеки подключений для Базы данных SQL и SQL Server](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Общие сведения о навигации по базам данных см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Защита базы данных](sql-database-security.md)
* [Центр обеспечения безопасности для ядра СУБД SQL Server и базы данных Azure SQL](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



<!--HONumber=Dec16_HO2-->


