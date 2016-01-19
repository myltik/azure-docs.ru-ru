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
   ms.date="01/11/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Создание хранилища данных SQL с помощью Powershell

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]Чтобы использовать Microsoft Azure Powershell с хранилищем данных SQL, требуется версия 1.0 или выше. Чтобы узнать версию, запустите командлет (Get-Module Azure).Version в PowerShell.

## Получение и запуск командлетов Azure PowerShell
Если вы еще не настроили модуль PowerShell, необходимо скачать и настроить его.

1. Чтобы загрузить модуль Azure PowerShell, запустите [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Чтобы запустить модуль, введите в окне запуска **Microsoft Azure PowerShell**.
3. Если вы еще не добавили свою учетную запись на компьютер, запустите следующий командлет. (Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell][]).

```
Add-AzureAccount
```

4. Выберите подписку, которую вы хотите использовать. Этот пример возвращает список названий подписок, а затем задает для подписки имя MySubscription. 

```
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```
   
## Создание хранилища данных SQL
Настроив PowerShell для своей учетной записи, вы можете выполнить следующую команду, чтобы развернуть новую базу данных в хранилище данных SQL.

```
New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

Ниже приведены необходимые параметры для этого командлета.

 + **RequestedServiceObjectiveName**: объем запрашиваемых единиц DWU в формате DWXXX. В настоящее время поддерживаются следующие значения: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 + **DatabaseName**: имя создаваемого хранилища данных SQL.
 + **ServerName**: имя сервера, который используется для создания (должен быть версии 12).
 + **ResourceGroupName**: используемая группа ресурсов. Чтобы найти доступные группы ресурсов, входящие в вашу подписку, используйте командлет Get-AzureResource.
 + **Edition**: необходимо установить выпуск DataWarehouse, чтобы создать хранилище данных SQL. 

Справку по командам см. в разделе [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx).

Сведения о параметрах см. в статье [Create Database (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx) (Создание базы данных (хранилище данных SQL Azure)).

## Дальнейшие действия
После завершения подготовки хранилища данных SQL вы можете [загрузить демонстрационные данные][] или ознакомиться с возможностями [разработки][], [загрузки][] и [переноса][].

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

<!---HONumber=AcomDC_0114_2016-->