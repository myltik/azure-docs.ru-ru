<properties
   pageTitle="Командлеты PowerShell для хранилища данных SQL Azure"
   description="Ознакомьтесь с основными командлетами PowerShell для хранилища данных SQL Azure, а также узнайте, как приостанавливать и возобновлять работу базы данных."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/02/2016"
   ms.author="barbkess;mausher;sonyama"/>

# Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL

Хранилищем данных SQL вы можете управлять с помощью командлетов Azure PowerShell или интерфейсов REST API.

Большинство команд, заданных для **базы данных SQL Azure**, используются также для **хранилища данных SQL**. Текущий список см. в статье [Командлеты SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx). Командлеты [Suspend-AzureRmSqlDatabase][] и [Resume-AzureRmSqlDatabase][] — это дополнения, предназначенные для хранилища данных SQL.

Точно так же интерфейсы REST API для **базы данных SQL Azure** можно использовать для экземпляров **хранилища данных SQL**. Текущий список см. в статье [Операции для баз данных Azure SQL](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## Приступая к работе с командлетами Azure PowerShell

1. Чтобы загрузить модуль Azure PowerShell, запустите [установщик веб-платформы Майкрософт](http://aka.ms/webpi-azps). Дополнительные сведения об этом установщике см. в статье [Установка и настройка Azure PowerShell][].
2. Чтобы запустить PowerShell, щелкните **Пуск** и введите **Windows PowerShell**.
3. В командной строке PowerShell выполните приведенные далее команды, чтобы войти в Azure Resource Manager Azure и выбрать свою подписку.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```


## Часто используемые командлеты PowerShell

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


## Примеры для хранилища данных SQL

Эти примеры предназначены для возможностей, которые применяются только к хранилищу данных SQL.

### [Suspend-AzureRmSqlDatabase][]

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

### [Resume-AzureRmSqlDatabase][]

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


## Дальнейшие действия
Дополнительные справочные сведения см. в обзоре [Общие справочные сведения о хранилище данных SQL][]. Дополнительные примеры PowerShell см. в указанных далее документах.
- [Создание хранилища данных SQL с помощью Powershell](sql-data-warehouse-get-started-provision-powershell.md)
- [Restore from snapshot (Восстановление из моментального снимка)](sql-data-warehouse-backup-and-restore-from-snapshot.md)
- [Geo-restore from snapshot (Геовосстановление из моментального снимка)](sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md)

<!--Image references-->

<!--Article references-->
[Общие справочные сведения о хранилище данных SQL]: sql-data-warehouse-overview-reference.md
[Установка и настройка Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureRmSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619337.aspx



<!--Other Web references-->

<!---HONumber=AcomDC_0406_2016-->