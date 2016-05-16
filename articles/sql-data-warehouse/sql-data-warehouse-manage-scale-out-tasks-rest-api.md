<properties
   pageTitle="Задачи управления масштабируемостью для хранилища данных SQL Azure (REST) | Microsoft Azure"
   description="Задачи API REST, относящиеся к масштабированию производительности для хранилища данных SQL Azure. Вы можете изменить вычислительные ресурсы путем настройки единиц DWU. Кроме того, можно приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
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
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Задачи управления масштабируемостью для хранилища данных SQL Azure (REST)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-overview-scalability.md)
- [Портал](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

Гибкое развертывание вычислительных ресурсов и памяти позволяет удовлетворять меняющиеся потребности рабочих нагрузок и сокращать затраты благодаря свертыванию ресурсов в периоды низкой нагрузки.

Этот набор задач использует API REST:

- для масштабирования производительности путем корректировки числа единиц DWU;
- для приостановки работы вычислительных ресурсов;
- для возобновления работы вычислительных ресурсов;

Дополнительные сведения см. в [обзоре масштабируемости производительности][].

<a name="scale-performance-bk"></a>

## Масштабирование производительности

[AZURE.INCLUDE [Описание единиц DWU масштабирования хранилища данных SQL](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Чтобы изменить DWU, используйте API REST для [Создание или обновление базы данных][]. В приведенном ниже примере для базы данных MySQLDW, размещенной на сервере MyServer, устанавливается цель уровня обслуживания DW1000. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## Приостановка работы вычислительных ресурсов

[AZURE.INCLUDE [Описание приостановки работы хранилища данных SQL](../../includes/sql-data-warehouse-pause-description.md)]

Чтобы приостановить базу данных, используйте API REST [Приостановка базы данных][]. В приведенном ниже примере приостанавливается работа базы данных с именем Database02, размещенной на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## Возобновление работы вычислительных ресурсов

[AZURE.INCLUDE [Описание возобновления работы хранилища данных SQL](../../includes/sql-data-warehouse-resume-description.md)]

Чтобы запустить базу данных, используйте API REST [Возобновление базы данных][]. В приведенном ниже примере запускается база данных с именем Database02, размещенная на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## Дальнейшие действия

Сведения о других задачах управления см. в статье [Средства управления][].

<!--Image references-->

<!--Article references-->
[Средства управления]: ./sql-data-warehouse-overview-manage.md
[обзоре масштабируемости производительности]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Приостановка базы данных]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Возобновление базы данных]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Создание или обновление базы данных]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->