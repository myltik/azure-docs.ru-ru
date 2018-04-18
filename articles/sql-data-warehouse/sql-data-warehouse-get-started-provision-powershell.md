---
title: "Создание хранилища данных SQL с помощью PowerShell | Документация Майкрософт"
description: "Создание хранилища данных SQL с помощью PowerShell"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: a763f1c600c1a3f37cb565a8eb7db3c3f27dcf75
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="create-sql-data-warehouse-using-powershell"></a>Создание хранилища данных SQL с помощью PowerShell
> [!div class="op_single_selector"]
> * [портале Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

В этой статье объясняется, как создать хранилище данных SQL с помощью PowerShell.

## <a name="prerequisites"></a>предварительным требованиям
Для начала работы необходимы перечисленные ниже компоненты и данные.

* **Учетная запись Azure.** Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][Azure Free Trial] или [денег на счете в Azure][MSDN Azure Credits] MSDN.
* **Сервер Azure SQL Server.** Дополнительные сведения см. в статье [Создание базы данных SQL Azure и отправка к ней запросов с помощью портала Azure][Create an Azure SQL database in the Azure Portal] или [Руководство по подготовке базы данных SQL Azure и доступу к ней с помощью портала PowerShell][Create an Azure SQL database with PowerShell].
* **Группа ресурсов**. Используйте ту же группу ресурсов, что и для Azure SQL Server, или [создайте новую группу ресурсов](../azure-resource-manager/resource-group-portal.md).
* **PowerShell версии 1.0.3 или более поздней.** Чтобы узнать версию, выполните командлет **Get-Module -ListAvailable -Name Azure**.  Последнюю версию можно установить с помощью [установщика веб-платформы Майкрософт][Microsoft Web Platform Installer].  Дополнительные сведения об установке последней версии Azure PowerShell см. в статье [Как установить и настроить Azure PowerShell][How to install and configure Azure PowerShell].

> [!NOTE]
> Создание хранилища данных SQL может привести к дополнительным расходам.  Дополнительные сведения о ценах на хранилище данных SQL см. на [этой странице][SQL Data Warehouse pricing].
>
>

## <a name="create-a-sql-data-warehouse"></a>Создание хранилища данных SQL
1. Откройте Windows PowerShell.
2. Используйте этот командлет для входа в диспетчер ресурсов Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
3. Выберите подписку, которую вы хотите использовать для текущего сеанса.

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Создайте базу данных. В этом примере база данных с именем mynewsqldw и целевым уровнем обслуживания DW400 будет создана на сервере с именем sqldwserver1, который находится в группе ресурсов с именем mywesteuroperesgp1.

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Ниже перечислены необходимые параметры.

* **RequestedServiceObjectiveName** — количество запрашиваемых единиц [DWU][DWU].  Поддерживаемые значения: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 и DW6000.
* **DatabaseName**: имя создаваемого хранилища данных SQL.
* **ServerName**: имя сервера, который используется для создания (должен быть версии 12).
* **ResourceGroupName**: используемая группа ресурсов.  Чтобы найти доступные группы ресурсов, входящие в вашу подписку, используйте командлет Get-AzureResource.
* **Edition**: для создания хранилища данных необходим выпуск DataWarehouse.

Необязательные параметры.

* **CollationName** — если параметры сортировки не указаны, по умолчанию используется SQL_Latin1_General_CP1_CI_AS.  Нельзя изменить параметры сортировки базы данных.
* **MaxSizeBytes**: по умолчанию максимальный размер базы данных составляет 10 ГБ.

Дополнительные сведения о параметрах см. в спецификации командлета [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] и в статье, посвященной [созданию базы данных (хранилище данных SQL Azure)][Create Database (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Дополнительная информация
Завершив подготовку хранилища данных SQL, вы можете попробовать [загрузить демонстрационные данные][loading sample data] или ознакомиться с возможностями [разработки][develop], [загрузки][load] или [переноса][migrate].

Если вы хотите больше узнать о том, как программно управлять хранилищем данных SQL, см. статью [Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL][PowerShell cmdlets and REST APIs].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
