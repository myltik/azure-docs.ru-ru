<properties
   pageTitle="Создание хранилища данных SQL с помощью TSQL | Microsoft Azure"
   description="Сведения о создании хранилища данных SQL Azure с помощью TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Создание базы данных хранилища данных SQL с помощью Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

В этой статье объясняется, как создать базу данных хранилища данных SQL с помощью Transact-SQL (TSQL).

## Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- Логический сервер SQL версии 12. Для создания хранилища данных SQL вам потребуется SQL Server версии 12. Если у вас нет логического сервера SQL версии 12, см. раздел **Настройка и создание сервера** в статье [Создание хранилища данных SQL][].
- приведенному. Бесплатный экземпляр Visual Studio см. на странице [Загрузки Visual Studio][].


> [AZURE.NOTE] Создание хранилища данных SQL может привести к дополнительным расходам. Дополнительные сведения о ценах см. на странице [Цены на хранилище данных SQL][].

## Создание базы данных с помощью Visual Studio

Если вы не знакомы с Visual Studio, см. статью [Подключение к хранилищу данных SQL с помощью Visual Studio][]. Сначала откройте обозреватель объектов SQL Server в Visual Studio и подключитесь к серверу, на котором будет размещена база данных хранилища данных SQL. После подключения можно создать хранилище данных SQL. Для этого выполните следующую команду для базы данных **master**: Эта команда создаст базу данных MySqlDwDb с целевой службой DW400 и позволит ей достичь максимального размера 10 ТБ.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Создание базы данных с помощью служебной программы sqlcmd

Также можно запустить ту же команду с помощью sqlcmd, выполнив следующую команду в командной строке.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Параметры **MAXSIZE** и **SERVICE\_OBJECTIVE** задают максимальное место на диске, которое может использовать база данных, и вычислительные ресурсы, выделяемые экземпляру хранилища данных. Целевая служба по сути представляет собой выделенные ресурсы ЦП и памяти, которые масштабируются линейно в зависимости от размера DWU.

Значение параметра MAXSIZE может составлять от 250 ГБ до 60 ТБ. Значение параметра SERVICE\_OBJECTIVE задается в диапазоне от DW100 до DW2000. Полный список всех допустимых значений параметров MAXSIZE и SERVICE\_OBJECTIVE см. в документации MSDN для инструкции [CREATE DATABASE][]. Параметры MAXSIZE и SERVICE\_OBJECTIVE также можно изменить с помощью команды T-SQL [ALTER DATABASE][]. При изменении параметра SERVICE\_OBJECTIVE следует соблюдать осторожность, так как это приводит к перезапуску служб, при котором отменяются все текущие запросы. При изменении параметра MAXSIZE такая предосторожность не требуется, так как это всего лишь операция с метаданными.

## Дальнейшие действия
После завершения подготовки хранилища данных SQL вы можете [загрузить демонстрационные данные][] или ознакомиться с возможностями [разработки][], [загрузки][] или [переноса][].

<!--Article references-->
[Создание хранилища данных SQL]: sql-data-warehouse-get-started-provision.md
[Подключение к хранилищу данных SQL с помощью Visual Studio]: sql-data-warehouse-get-started-connect.md
[переноса]: sql-data-warehouse-overview-migrate.md
[разработки]: sql-data-warehouse-overview-develop.md
[загрузки]: sql-data-warehouse-overview-load.md
[загрузить демонстрационные данные]: sql-data-warehouse-get-started-manually-load-samples.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Цены на хранилище данных SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Загрузки Visual Studio]: https://www.visualstudio.com/downloads/download-visual-studio-vs

<!---HONumber=AcomDC_0511_2016-->