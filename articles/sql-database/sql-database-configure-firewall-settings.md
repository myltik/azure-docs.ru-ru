---
title: "Настройка правила брандмауэра уровня сервера базы данных SQL | Документация Майкрософт"
description: "Сведения о настройке брандмауэра для IP-адресов, через которые осуществляется доступ к Azure SQL Server."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: ad72adf626fb5f59f239f57ff0158db101017a1b
ms.openlocfilehash: 7925663bd4233e368f54b38c94888ac1b0f5dcab


---
# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure
> [!div class="op_single_selector"]
> * [Обзор](sql-database-firewall-configure.md)
> * [Портал Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Для разрешения подключений к серверам и базам данных сервер SQL Azure использует правила брандмауэра. На логическом сервере SQL Azure для базы данных master или пользовательской базы данных можно определить параметры брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных. В этой статье рассматриваются правила брандмауэра уровня сервера.

> [!IMPORTANT]
> Чтобы приложения из Azure могли подключаться к серверу SQL Azure, необходимо разрешить подключения Azure. Принцип действия правил брандмауэра описан в статье [Настройка правил брандмауэра для базы данных SQL Azure: обзор](sql-database-firewall-configure.md). Если вы создаете подключения внутри облака Azure, вам, возможно, придется открыть дополнительные TCP-порты. Дополнительные сведения см. в разделе **Версия 12 базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md).
> 
> 

**Рекомендация.** Используйте правила брандмауэра уровня сервера для администраторов, если у вас много баз данных с одинаковыми требованиями доступа и вы не хотите тратить время на настройку каждой базы данных по отдельности. Корпорация Майкрософт рекомендует использовать правила брандмауэра уровня базы данных всегда, когда это возможно, чтобы повысить уровень безопасности и сделать базу данных более портативной.

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Управление существующими правилами брандмауэра с помощью портала Azure
Повторите действия для управлению правилами брандмауэра на уровне сервера.

* Чтобы добавить текущий компьютер, щелкните "Добавить IP-адрес клиента".
* Чтобы добавить дополнительные IP-адреса, введите имя правила, начальный IP-адрес и конечный IP-адрес.
* Чтобы изменить существующее правило, измените значения во всех полях в данном правиле.
* Чтобы удалить существующее правило, наведите указатель мыши на правило и дождитесь появления X в конце строки. Щелкните X, чтобы удалить правило.

Щелкните **Сохранить** , чтобы сохранить изменения.

## <a name="next-steps"></a>Дальнейшие действия
Практическое руководство по использованию службы Transact-SQL для создания правил брандмауэра уровня сервера и уровня базы данных см. в статье [Настройка правил брандмауэра уровня сервера базы данных SQL Azure и уровня базы данных SQL Azure с помощью T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Практические руководства по созданию правил брандмауэра уровня сервера с помощью других методов см. в следующих разделах. 

* [Настройка правил брандмауэра уровня сервера базы данных SQL с помощью PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью REST API](sql-database-configure-firewall-settings-rest.md)

Учебник по созданию базы данных SQL доступен в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md).   

Сведения о добавлении дополнительных пользователей, которые могут подключаться к базам данных, см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Защита базы данных](sql-database-security.md)   
* [Центр обеспечения безопасности для ядра СУБД SQL Server и базы данных Azure SQL](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Nov16_HO5-->


