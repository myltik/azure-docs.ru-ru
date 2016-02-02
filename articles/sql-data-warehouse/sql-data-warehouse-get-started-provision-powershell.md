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
   ms.date="01/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Создание хранилища данных SQL с помощью Powershell

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Получение и запуск командлетов Azure PowerShell

> [AZURE.NOTE]  Чтобы использовать Microsoft Azure Powershell с хранилищем данных SQL, следует загрузить и установить последнюю версию Azure PowerShell с командлетами ARM. Чтобы проверить версию, запустите `Get-Module -ListAvailable -Name Azure`. В этой статье используются командлеты, выполняемые в Azure PowerShell 1.0.3 и более поздних версий.

Если вы еще не настроили модуль PowerShell, необходимо скачать и настроить его.

1. Чтобы загрузить модуль Azure PowerShell, запустите [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Чтобы запустить модуль, введите в окне запуска **Microsoft Azure PowerShell**.
3. Используйте этот командлет для входа в диспетчер ресурсов Azure. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell][].

	```
	Login-AzureRmAccount
	```

4. Выберите подписку, которую вы хотите использовать для текущего сеанса.

	```
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```
   
## Создание базы данных хранилища данных SQL
Чтобы развернуть хранилище данных SQL, используйте командлет New-AzureRmSQLDatabase. Прежде чем выполнять команду, убедитесь в наличии следующих необходимых компонентов.

### Предварительные требования

- Сервер Azure SQL Server V12 для размещения базы данных.
- Имя группы ресурсов для SQL Server.

### Команда развертывания

Эта команда развернет новую базу данных в хранилище данных SQL.

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

В этом примере новая база данных с именем mynewsqldw1 и целевым уровнем обслуживания DW400 будет развернута на сервере с именем sqldwserver1, который находится в группе ресурсов с именем mywesteuroperesgp1.

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

Ниже приведены необходимые параметры для этого командлета.

 + **RequestedServiceObjectiveName**: объем запрашиваемых единиц DWU в формате DWXXX. В настоящее время поддерживаются следующие значения: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 + **DatabaseName**: имя создаваемого хранилища данных SQL.
 + **ServerName**: имя сервера, который используется для создания (должен быть версии 12).
 + **ResourceGroupName**: используемая группа ресурсов. Чтобы найти доступные группы ресурсов, входящие в вашу подписку, используйте командлет Get-AzureResource.
 + **Edition**: необходимо установить выпуск DataWarehouse, чтобы создать хранилище данных SQL. 

Справку по командам см. в разделе [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx).

Сведения о параметрах см. в статье [Create Database (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx) (Создание базы данных (хранилище данных Azure SQL)).

## Дальнейшие действия
Подготовив хранилище данных SQL, вы можете [загрузить демонстрационные данные][] или ознакомиться с возможностями [разработки][], [загрузки][] и [переноса][].

Если вы хотите подробнее узнать о том, как программно управлять хранилищем данных SQL, обратитесь к нашей документации по [Powershell][] или [REST API][].



<!--Image references-->

<!--Article references-->
[переноса]: ./sql-data-warehouse-overview-migrate.md
[разработки]: ./sql-data-warehouse-overview-develop/.md
[загрузить демонстрационные данные]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[Установка и настройка Azure PowerShell]: ./powershell-install-configure.md

<!---HONumber=AcomDC_0128_2016-->