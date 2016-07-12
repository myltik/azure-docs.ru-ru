<properties
   pageTitle="Создание хранилища данных SQL с помощью Powershell | Microsoft Azure"
   description="Создание хранилища данных SQL с помощью Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Создание хранилища данных SQL с помощью Powershell

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Предварительные требования
Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.

- **Учетная запись Azure.** Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][] или [кредитов Azure MSDN][].
- **Сервер SQL Azure версии 12.** См. разделы, посвященные [созданию логического сервера базы данных SQL с помощью портала Azure][] или [с помощью PowerShell][].
- **Имя группы ресурсов.** Используйте ту же группу ресурсов, что и для сервера SQL Azure версии 12, или создайте новую группу ресурсов, следуя инструкциям из [этой статьи][].
- **PowerShell версии 1.0.3 или выше.** Чтобы узнать версию, выполните командлет **Get-Module -ListAvailable -Name Azure**. Последнюю версию можно установить с помощью [установщика веб-платформы Майкрософт][]. Дополнительную информацию об установке последней версии Azure PowerShell см. в статье [Установка и настройка Azure PowerShell][].

> [AZURE.NOTE] Создание хранилища данных SQL может привести к дополнительным расходам. Дополнительные сведения о ценах см. на странице [Цены на хранилище данных SQL][].

## Создание базы данных хранилища данных SQL
1. Откройте Windows PowerShell.
2. Используйте этот командлет для входа в диспетчер ресурсов Azure.

	```Powershell
	Login-AzureRmAccount
	```
	
3. Выберите подписку, которую вы хотите использовать для текущего сеанса.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  Создайте базу данных. В этом примере новая база данных с именем mynewsqldw и целевым уровнем обслуживания DW400 будет создана на сервере с именем sqldwserver1, который находится в группе ресурсов с именем mywesteuroperesgp1.

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

Список параметров, необходимых для этого командлета:

- **RequestedServiceObjectiveName**: количество запрашиваемых единиц [DWU][]. Поддерживаемые значения: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500 и DW2000.
- **DatabaseName**: имя создаваемого хранилища данных SQL.
- **ServerName**: имя сервера, который используется для создания (должен быть версии 12).
- **ResourceGroupName**: используемая группа ресурсов. Чтобы найти доступные группы ресурсов, входящие в вашу подписку, используйте командлет Get-AzureResource.
- **Edition**: необходимо установить выпуск DataWarehouse, чтобы создать хранилище данных SQL.

Дополнительные сведения о параметрах см. в статье, посвященной [созданию базы данных (хранилище данных SQL Azure)][]. Справку по командам см. в статье [New-AzureRmSqlDatabase][].

## Дальнейшие действия
После завершения подготовки хранилища данных SQL вы можете попробовать [загрузить демонстрационные данные][] или ознакомиться с возможностями [разработки][], [загрузки][] или [миграции][].

Если вы хотите больше узнать о том, как программно управлять хранилищем данных SQL, см. статью [Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[миграции]: ./sql-data-warehouse-overview-migrate.md
[разработки]: ./sql-data-warehouse-overview-develop.md
[загрузки]: ./sql-data-warehouse-load-with-bcp.md
[загрузить демонстрационные данные]: ./sql-data-warehouse-get-started-load-sample-databases.md
[Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Установка и настройка Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[созданию логического сервера базы данных SQL с помощью портала Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[с помощью PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[этой статьи]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[созданию базы данных (хранилище данных SQL Azure)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[установщика веб-платформы Майкрософт]: https://aka.ms/webpi-azps
[Цены на хранилище данных SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[бесплатной пробной версии Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[кредитов Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0706_2016-->