---
title: "Создание хранилища данных SQL с помощью PowerShell | Документация Майкрософт"
description: "Создание хранилища данных SQL с помощью PowerShell"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3d13d4a0dd1d6e0b7361a57e167b06f0b717bfb4


---
# <a name="create-sql-data-warehouse-using-powershell"></a>Создание хранилища данных SQL с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

В этой статье объясняется, как создать хранилище данных SQL с помощью PowerShell.

## <a name="prerequisites"></a>Предварительные требования
Для начала работы необходимы перечисленные ниже компоненты и данные.

* **Учетная запись Azure.** Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][Бесплатная пробная версия Azure] или [кредитов Azure MSDN][Кредиты Azure MSDN].
* **Azure SQL Server.** Дополнительные сведения см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure][Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure] или [Создание базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell][Создание базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell].
* **Группа ресурсов.** Используйте ту же группу ресурсов, что и для Azure SQL Server, или [создайте группу ресурсов][cоздание группы ресурсов].
* **PowerShell версии 1.0.3 или более поздней.** Чтобы узнать версию, выполните командлет **Get-Module -ListAvailable -Name Azure**.  Последнюю версию можно установить с помощью [установщика веб-платформы Майкрософт][установщик веб-платформы Майкрософт].  Дополнительные сведения об установке последней версии Azure PowerShell см. в статье [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

> [!NOTE]
> Создание хранилища данных SQL может привести к дополнительным расходам.  Дополнительные сведения о ценах см. на странице с [ценами на хранилище данных SQL][цены на хранилище данных SQL].
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

* **RequestedServiceObjectiveName** — количество запрашиваемых единиц [DWU][DWU].  Поддерживаемые значения: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 и DW6000.
* **DatabaseName**: имя создаваемого хранилища данных SQL.
* **ServerName**: имя сервера, который используется для создания (должен быть версии 12).
* **ResourceGroupName**: используемая группа ресурсов.  Чтобы найти доступные группы ресурсов, входящие в вашу подписку, используйте командлет Get-AzureResource.
* **Edition**: для создания хранилища данных необходим выпуск DataWarehouse.

Необязательные параметры.

* **CollationName** — если параметры сортировки не указаны, по умолчанию используется SQL_Latin1_General_CP1_CI_AS.  Нельзя изменить параметры сортировки базы данных.
* **MaxSizeBytes**: по умолчанию максимальный размер базы данных составляет 10 ГБ.

Дополнительные сведения о параметрах см. в спецификации командлета [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] и в статье, посвященной [созданию базы данных (хранилище данных SQL Azure)][создание базы данных (хранилище данных SQL Azure)].

## <a name="next-steps"></a>Дальнейшие действия
После завершения подготовки хранилища данных SQL попробуйте [загрузить в него демонстрационные данные][загрузка демонстрационных данных] или ознакомьтесь с возможностями [разработки][разработка], [загрузки][загрузка] или [миграции][миграция].

Дополнительные сведения о программном управлении хранилищем данных SQL см. в статье [Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL][Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[переноса]: ./sql-data-warehouse-overview-migrate.md
[разработки]: ./sql-data-warehouse-overview-develop.md
[загрузки]: ./sql-data-warehouse-load-with-bcp.md
[Загрузка демонстрационных данных]: ./sql-data-warehouse-load-sample-databases.md
[Интерфейсы REST API и командлеты PowerShell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[Правила брандмауэра]: ../sql-database-configure-firewall-settings.md

[Установка и настройка Azure PowerShell]: ../powershell-install-configure.md
[Создание хранилища данных SQL на портале Azure]: ./sql-data-warehouse-get-started-provision.md
[созданию логического сервера базы данных SQL с помощью портала Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[создайте новую группу ресурсов]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Создание базы данных (хранилище данных SQL Azure)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Установщик веб-платформы Майкрософт]: https://aka.ms/webpi-azps
[ценами на хранилище данных SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[бесплатной пробной версии Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[деньги на счете в Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


