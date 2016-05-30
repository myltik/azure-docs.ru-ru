<properties
   pageTitle="Управление вычислительными ресурсами в хранилище данных SQL Azure (PowerShell) | Microsoft Azure"
   description="Задачи PowerShell для управления вычислительными ресурсами. Масштабирование вычислительных ресурсов путем изменения числа единиц DWU. Кроме того, можно приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
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
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# Управление вычислительными ресурсами в хранилище данных SQL Azure (PowerShell)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-overview-manage-compute.md)
- [Портал](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Масштабирование производительности путем развертывания вычислительных ресурсов и памяти для удовлетворения меняющихся потребностей рабочих нагрузок. Снижение затрат путем свертывания ресурсов в периоды низкой загрузки или полной приостановки вычислений.

Этот набор задач использует портал Azure:

- Масштабирование вычислительных ресурсов
- Приостановка работы вычислительных ресурсов
- Возобновление работы вычислительных ресурсов

Дополнительные сведения см. в [обзоре управления вычислительными ресурсами][].


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

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## Масштабирование вычислительных ресурсов

[AZURE.INCLUDE [Описание единиц DWU масштабирования хранилища данных SQL](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Чтобы изменить число единиц DWU, используйте командлет PowerShell [Set-AzureRmSqlDatabase][]. В приведенном ниже примере для базы данных MySQLDW, размещенной на сервере MyServer, устанавливается цель уровня обслуживания DW1000.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## Приостановка работы вычислительных ресурсов

[AZURE.INCLUDE [Описание приостановки работы хранилища данных SQL](../../includes/sql-data-warehouse-pause-description.md)]

Чтобы приостановить работу базы данных, используйте командлет [Suspend-AzureRmSqlDatabase][]. В приведенном ниже примере приостанавливается работа базы данных с именем Database02, размещенной на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

> [AZURE.NOTE] Обратите внимание, что если вашим сервером является foo.database.windows.net, в командлетах PowerShell в качестве -ServerName используйте значение "foo".

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
В следующем примере, являющемся вариантом предыдущего, база данных извлекается в объект $database. Затем объект передается по конвейеру в командлет [Suspend-AzureRmSqlDatabase][]. Результаты сохраняются в объекте resultDatabase. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## Возобновление работы вычислительных ресурсов

[AZURE.INCLUDE [Описание возобновления работы хранилища данных SQL](../../includes/sql-data-warehouse-resume-description.md)]

Чтобы запустить базу данных, используйте командлет [Resume-AzureRmSqlDatabase][]. В приведенном ниже примере запускается база данных с именем Database02, размещенная на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

В следующем примере, являющемся вариантом предыдущего, база данных извлекается в объект $database. Затем объект передается в командлет [Resume-AzureRmSqlDatabase][], и результаты сохраняются в объекте $resultDatabase. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## Дальнейшие действия

Сведения о других задачах управления см. в статье [Средства управления][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Средства управления]: ./sql-data-warehouse-overview-manage.md
[Performance scalability overview]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->