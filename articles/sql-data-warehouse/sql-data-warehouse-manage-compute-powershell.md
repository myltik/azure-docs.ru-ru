---
title: "Управление вычислительными ресурсами в хранилище данных SQL Azure (PowerShell) | Документация Майкрософт"
description: "Задачи PowerShell для управления вычислительными ресурсами. Масштабирование вычислительных ресурсов путем изменения числа единиц DWU. Кроме того, можно приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: f9814fc0011153a25489d60d696bb567edfcebc4
ms.openlocfilehash: a4cde433850ab3627f870b51f5a897c6f846c2f0


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Управление вычислительными ресурсами в хранилище данных SQL Azure (PowerShell)
> [!div class="op_single_selector"]
> * [Обзор](sql-data-warehouse-manage-compute-overview.md)
> * [Портал](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

## <a name="before-you-begin"></a>Перед началом работы
### <a name="install-the-latest-version-of-azure-powershell"></a>Установка последней версии Azure PowerShell
> [!NOTE]
> Чтобы использовать Azure PowerShell с хранилищем данных SQL, установите Azure PowerShell 1.0.3 или более поздней версии.  Чтобы узнать текущую версию, выполните команду **Get-Module -ListAvailable -Name Azure**. Последнюю версию можно установить с помощью [установщика веб-платформы Майкрософт][Microsoft Web Platform Installer].  Дополнительные сведения см. в статье [Установка и настройка служб Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Приступая к работе с командлетами Azure PowerShell
Чтобы начать работу:

1. Откройте Azure PowerShell.
2. В командной строке PowerShell выполните приведенные далее команды, чтобы войти в Azure Resource Manager Azure и выбрать свою подписку.
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Масштабирование вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Чтобы изменить число единиц DWU, используйте командлет PowerShell [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]. В приведенном ниже примере для базы данных MySQLDW, размещенной на сервере MyServer, устанавливается цель уровня обслуживания DW1000.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Приостановка работы вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Чтобы приостановить работу базы данных, используйте командлет [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. В приведенном ниже примере приостанавливается работа базы данных с именем Database02, размещенной на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

> [!NOTE]
> Обратите внимание, что если вашим сервером является foo.database.windows.net, в командлетах PowerShell в качестве -ServerName используйте значение "foo".
> 
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
В следующем примере, являющемся вариантом предыдущего, база данных извлекается в объект $database. Затем объект передается по конвейеру в командлет [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. Результаты сохраняются в объекте resultDatabase. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Возобновление работы вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Чтобы запустить базу данных, используйте командлет [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]. В приведенном ниже примере запускается база данных с именем Database02, размещенная на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

В следующем примере, являющемся вариантом предыдущего, база данных извлекается в объект $database. Затем объект передается в командлет [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase], и результаты сохраняются в объекте $resultDatabase. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других задачах управления см. в статье [Управление базами данных в хранилище данных SQL Azure][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/



<!--HONumber=Jan17_HO3-->


