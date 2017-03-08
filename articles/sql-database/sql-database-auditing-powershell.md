---
title: "PowerShell: управление аудитом базы данных SQL Azure | Документация Майкрософт"
description: "Сведения о настройке аудита базы данных SQL Azure с помощью PowerShell для отслеживания всех событий базы данных и записи их в журнал аудита для вашей учетной записи хранения Azure."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>Настройка аудита базы данных SQL и управление им с помощью PowerShell

Здесь описывается настройка аудита и управление им с помощью PowerShell. Сведения о настройке аудита и управлении им с помощью портала Azure см. в разделе [Настройка аудита базы данных SQL и управление им на портале Azure](sql-database-auditing-portal.md). Сведения о настройке аудита и управлении им с помощью REST API см. в разделе [Настройка аудита базы данных SQL и управление им с помощью REST API](sql-database-auditing-rest.md).

Общие сведения об аудите базы данных SQL см. в статье [Приступая к работе с аудитом базы данных SQL](sql-database-auditing.md).

## <a name="powershell-cmdlets"></a>Командлеты PowerShell

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о настройке аудита базы данных и управлении им с помощью портала Azure см. в разделе [Настройка аудита базы данных SQL и управление им на портале Azure](sql-database-auditing-portal.md). 
* Сведения о настройке аудита базы данных и управлении им с помощью PowerShell см. в разделе [Настройка аудита базы данных SQL и управление им с помощью REST API](sql-database-auditing-rest.md).
* Общие сведения об аудите базы данных см. в статье [Приступая к работе с аудитом базы данных SQL](sql-database-auditing.md).


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

