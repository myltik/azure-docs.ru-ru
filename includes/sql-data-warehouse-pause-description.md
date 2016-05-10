
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
Для сокращения затрат можно приостанавливать и возобновлять работу вычислительных ресурсов по требованию. Например, если база данных не будет использоваться ночью и по выходным, ее работу можно приостанавливать на это время и возобновлять днем. Когда работа база данных приостановлена, оплата за единицы DWU не взимается.

При приостановке работы базы данных происходит следующее.

- Вычислительные ресурсы и ресурсы памяти возвращаются в пул доступных ресурсов в центре данных.
- В течение периода приостановки затраты на DWU отсутствуют.
- Хранилище данных не затрагивается, и ваши данные остаются без изменений. 
- Хранилище данных SQL отменяет все выполняющиеся и поставленные в очередь операции.

<!---HONumber=AcomDC_0427_2016-->