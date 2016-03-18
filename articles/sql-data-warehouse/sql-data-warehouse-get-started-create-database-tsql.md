<properties
   pageTitle="Создание хранилища данных SQL с помощью TSQL | Microsoft Azure"
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
   ms.date="03/03/2016"
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
- приведенному. Бесплатный экземпляр Visual Studio см. на странице [Загрузки Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).
- Логический сервер SQL версии 12. Для создания хранилища данных SQL вам потребуется SQL Server версии 12. Если у вас нет логического сервера SQL версии 12, вы узнаете, как его создать, из [руководства по порталу Azure][].

## Создание базы данных с помощью Visual Studio

В данной статье не рассматривается правильная настройка базы данных и подключение к ней с помощью Visual Studio. Полное описание этих процедур см. в документации по [подключению и созданию запросов][]. Сначала откройте обозреватель объектов SQL Server в Visual Studio и подключитесь к серверу, который будет использоваться для создания базы данных хранилища данных SQL. Теперь вы можете создать хранилище данных SQL. Для этого запустите следующую команду для основной базы данных:

        CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>);

## Создание базы данных с помощью служебной программы sqlcmd

Вы также можете создать хранилище данных SQL, открыв командную строку и выполнив следующую команду:

        sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>)"

Во время выполнения инструкций TSQL запишите параметры MAXSIZE и SERVICE\_OBJECTIVE. Они определяют размер исходного хранилища и вычисляют экземпляр, предназначенный для вашего хранилища данных. Параметр MAXSIZE принимает следующие значения (рекомендуется выбрать большой размер, чтобы оставить место для роста базы данных): 250 ГБ, 500 ГБ, 750 ГБ, 1024 ГБ, 5120 ГБ, 10 240 ГБ, 20 480 ГБ, 30 720 ГБ, 40 960 ГБ, 51 200 ГБ.

Значение SERVICE\_OBJECTIVE определяет количество DWU, с которым начнет работать экземпляр, и принимает следующие значения: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000. Сведения о влиянии этих параметров на стоимость см. на нашей [странице цен][].

## Дальнейшие действия
После завершения подготовки хранилища данных SQL вы можете [загрузить демонстрационные данные][] или ознакомиться с возможностями [разработки][], [загрузки][] и [переноса][].

[руководства по порталу Azure]: ./sql-data-warehouse-get-started-provision.md
[подключению и созданию запросов]: ./sql-data-warehouse-get-started-connect.md
[переноса]: ./sql-data-warehouse-overview-migrate.md
[разработки]: ./sql-data-warehouse-overview-develop.md
[загрузки]: ./sql-data-warehouse-overview-load.md
[загрузить демонстрационные данные]: ./sql-data-warehouse-get-started-manually-load-samples.md
[странице цен]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/

<!---HONumber=AcomDC_0309_2016-->