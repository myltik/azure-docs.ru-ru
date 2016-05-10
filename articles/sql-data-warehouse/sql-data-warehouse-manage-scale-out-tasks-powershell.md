<properties
   pageTitle="Задачи по развертыванию и свертыванию вычислительных ресурсов в хранилище данных Azure SQL | Microsoft Azure"
   description="Узнайте, как приостанавливать и возобновлять работу вычислительных ресурсов для базы данных хранилища данных Azure SQL и как развертывать и свертывать единицы DWU для цели уровня обслуживания. Для выполнения этих задач используются командлеты Azure PowerShell."
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
   ms.date="04/21/2016"
   ms.author="barbkess;sonyama"/>

# Задачи по развертыванию и свертыванию вычислительных ресурсов в хранилище данных Azure SQL

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)


Гибкое развертывание вычислительных ресурсов и памяти позволяет удовлетворять меняющиеся потребности рабочих нагрузок и сокращать затраты благодаря свертыванию ресурсов в периоды низкой нагрузки.

В этом наборе задач командлеты PowerShell используются в следующих целях:

- для приостановки работы вычислительных ресурсов;
- для возобновления работы вычислительных ресурсов;
- для изменения вычислительных ресурсов путем настройки единиц DWU.

## Перед началом работы

### Установка последней версии Azure PowerShell

> [AZURE.NOTE]  Чтобы использовать Azure PowerShell с хранилищем данных SQL, установите Azure PowerShell 1.0.3 или более поздней версии. Чтобы узнать текущую версию, выполните команду **Get-Module -ListAvailable -Name Azure**. Установить последнюю версию можно на странице [Установщик веб-платформы Майкрософт][]. Дополнительные сведения см. в статье [Установка и настройка служб Azure PowerShell][].

### Приступая к работе с командлетами Azure PowerShell

Чтобы начать работу:

1. Откройте Azure PowerShell. 
2. В командной строке PowerShell выполните приведенные далее команды, чтобы войти в Azure Resource Manager Azure и выбрать свою подписку.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## Задача 1. Приостановка работы вычислительных ресурсов

[AZURE.INCLUDE [Описание приостановки работы хранилища данных SQL](../../includes/sql-data-warehouse-pause-description.md)]

Чтобы приостановить работу базы данных, используйте командлет [Suspend-AzureRmSqlDatabase][]. В приведенном ниже примере приостанавливается работа базы данных с именем Database02, размещенной на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

> [AZURE.NOTE] Обратите внимание, что если вашим сервером является foo.database.windows.net, в командлетах PowerShell в качестве -ServerName используйте значение "foo".

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
В следующем примере, являющемся вариантом предыдущего, база данных извлекается в объект $database. Затем объект передается по конвейеру в командлет [Suspend-AzureRmSqlDatabase][]. Результаты сохраняются в объекте resultDatabase. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Задача 2. Возобновление работы вычислительных ресурсов

[AZURE.INCLUDE [Описание возобновления работы хранилища данных SQL](../../includes/sql-data-warehouse-resume-description.md)]

Чтобы запустить базу данных, используйте командлет [Resume-AzureRmSqlDatabase][]. В приведенном ниже примере запускается база данных с именем Database02, размещенная на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

В следующем примере, являющемся вариантом предыдущего, база данных извлекается в объект $database. Затем объект передается в командлет [Resume-AzureRmSqlDatabase][], и результаты сохраняются в объекте $resultDatabase. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```


## Задача 3. Единицы DWU масштабирования

[AZURE.INCLUDE [Описание единиц DWU масштабирования хранилища данных SQL](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Чтобы изменить DWU, используйте командлет PowerShell [Set-AzureRmSqlDatabase][]. В приведенном ниже примере для базы данных MySQLDW, размещенной на сервере MyServer, устанавливается цель уровня обслуживания DW1000.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

## Дальнейшие действия

Сведения о других задачах управления см. в статье [Средства управления][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Средства управления]: ./sql-data-warehouse-overview-manage.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0427_2016-->