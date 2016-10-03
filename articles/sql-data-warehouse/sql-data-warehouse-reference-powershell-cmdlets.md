<properties
   pageTitle="Командлеты PowerShell для хранилища данных SQL Azure"
   description="Ознакомьтесь с основными командлетами PowerShell для хранилища данных SQL Azure, а также узнайте, как приостанавливать и возобновлять работу базы данных."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL

Многими задачами по администрированию хранилища данных SQL можно управлять с помощью командлетов Azure PowerShell или интерфейсов API REST. Ниже представлены некоторые примеры использования команд PowerShell для автоматизации обычных задач в хранилище данных SQL. Хорошие примеры использования REST приведены в разделе [Управление вычислительными ресурсами в хранилище данных SQL Azure (REST)][].

> [AZURE.NOTE]  Чтобы использовать Azure PowerShell с хранилищем данных SQL, установите Azure PowerShell 1.0.3 или более поздней версии. Чтобы узнать версию, выполните командлет **Get-Module -ListAvailable -Name Azure**. Последнюю версию можно установить с [установщика веб-платформы Майкрософт][]. Дополнительные сведения об установке последней версии Azure PowerShell см. в статье [Установка и настройка Azure PowerShell][].

## Приступая к работе с командлетами Azure PowerShell

1. Откройте Windows PowerShell.
2. В командной строке PowerShell выполните приведенные далее команды, чтобы войти в Azure Resource Manager Azure и выбрать свою подписку.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## Пример приостановки хранилища данных SQL

Приостанавливает базу данных с именем Database02, размещенную на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Как вариант, в этом примере полученный объект передается в командлет [Suspend-AzureRmSqlDatabase][]. В результате база данных приостанавливается. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Пример запуска хранилища данных SQL

Возобновляет работу базы данных с именем Database02, размещенную на сервере с именем Server01. Сервер находится в группе ресурсов с именем ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Как вариант, в этом примере извлекается база данных с именем Database02 с сервера Server01, который находится в группе ресурсов с именем ResourceGroup1. Полученный объект передается в командлет [Resume-AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Обратите внимание, что если вашим сервером является foo.database.windows.net, в командлетах PowerShell в качестве -ServerName используйте значение "foo".

## Часто используемые PowerShell командлеты

Перечисленные далее командлеты PowerShell часто используются для работы с хранилищем данных SQL Azure.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [New-AzureRmSqlDatabase][]
- [Remove-AzureRmSqlDatabase][]
- [Restore-AzureRmSqlDatabase][]
- [Resume-AzureRmSqlDatabase][]
- [Select-AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspend-AzureRmSqlDatabase][]

## Дальнейшие действия
Дополнительные примеры PowerShell см. в указанных далее документах.

- [Создание хранилища данных SQL с помощью Powershell][]
- [Восстановление базы данных][]

Список всех задач, которые можно автоматизировать с помощью PowerShell, см. в разделе [Azure SQL Database Cmdlets][] \(Командлеты базы данных SQL Azure). Список задач, которые можно автоматизировать с помощью REST, см. в статье [Операции для баз данных SQL Azure][].

<!--Image references-->

<!--Article references-->
[Установка и настройка Azure PowerShell]: ./powershell-install-configure.md
[Создание хранилища данных SQL с помощью Powershell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Восстановление базы данных]: ./sql-data-warehouse-restore-database-powershell.md
[Управление вычислительными ресурсами в хранилище данных SQL Azure (REST)]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://msdn.microsoft.com/library/mt574084.aspx
[Операции для баз данных SQL Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[установщика веб-платформы Майкрософт]: https://aka.ms/webpi-azps

<!----HONumber=AcomDC_0817_2016-->
