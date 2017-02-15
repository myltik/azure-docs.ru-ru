---
title: "Перенос базы данных SQL Server в базу данных SQL Azure | Документация Майкрософт"
description: "Узнайте, как выполнить миграцию локальной базы данных SQL Server в базу данных SQL Azure в облаке. Используйте средства миграции баз данных для проверки совместимости перед миграцией базы данных."
keywords: "миграция базы данных, миграция базы данных SQL Server, средства миграции базы данных, миграция базы данных, миграция базы данных SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 61fb027dfdd5830d87fe4fcfff57f685db71475e


---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Миграция базы данных SQL Server в базу данных SQL в облаке
Из этой статьи вы узнаете, как перенести локальную базу данных SQL Server 2005 или более поздней версии в Базу данных SQL Azure. В процессе переноса базы данных выполняется перенос схемы и данных из базы данных SQL Server в текущей среде в базу данных SQL. Для успешного выполнения существующая база данных должна предварительно пройти тест на совместимость. Начиная с версии 12 у всех последующих версий базы данных SQL функции, не связанные с операциями уровня сервера и операциями между базами данных, [практически не отличаются](sql-database-features.md). Базы данных и приложения, использующие [частично или полностью неподдерживаемые функции](sql-database-transact-sql-information.md) , потребуют небольшой доработки для устранения этих несовместимостей перед переносом базы данных SQL Server.

Ниже перечислены действия, выполняемые при переносе.

* **Тестирование на совместимость:** проверьте совместимость базы данных с базой данных SQL. 
* **Устранение проблем совместимости, если таковые имеются**: если проверка не пройдена, необходимо устранить ошибки проверки.  
* **Выполнение миграции** : после исправления ошибок совместимости можно выполнить миграцию одним или несколькими способами. 

SQL Server предоставляет несколько методов для решения каждой из этих задач. В этой статье представлен обзор методов, доступных для решения каждой задачи. Действия и методы показаны на следующей схеме.

  ![Схема переноса VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> Сведения о переносе в базу данных SQL Azure баз данных, отличных от SQL Server, в том числе Microsoft Access, Sybase, MySQL Oracle и DB2, см. в блоге, посвященном [помощнику по миграции SQL Server](http://blogs.msdn.com/b/ssma/).
> 
> 

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Средства миграции баз данных проверяют совместимость базы данных SQL Server с базой данных SQL.
Чтобы проверить отсутствие проблем совместимости базы данных SQL перед началом процесса миграции, используйте один из следующих методов.

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* [SQL Server Data Tools для Visual Studio (SSDT).](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) SSDT использует последние правила совместимости для обнаружения несовместимости с базой данных SQL версии 12. Если обнаружены проблемы с совместимостью, их можно исправить прямо в этом инструменте. Это рекомендуемый метод для проверки и устранения проблем совместимости с Базой данных SQL версии 12. 
* [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)— это программа командной строки, которая проверяет наличие проблем совместимости и создает отчет об обнаруженных проблемах. При использовании этого средства убедитесь, что используется самая последняя версия правил совместимости. При обнаружении ошибок необходимо использовать другое средство для устранения обнаруженных проблем с совместимостью — рекомендуется использовать SSDT.  
* [Мастер экспорта данных уровня приложения в SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)выявляет ошибки и выводит их на экран. Если ошибок не обнаружено, можно выполнить миграцию в базу данных SQL. При обнаружении ошибок необходимо использовать другое средство для устранения обнаруженных проблем с совместимостью — рекомендуется использовать SSDT.
* [Мастер миграции SQL Azure (SAMW).](sql-database-cloud-migrate-fix-compatibility-issues.md) SAMW — это инструмент codeplex, который использует правила совместимости базы данных SQL Azure версии 11 для обнаружения несовместимости для базы данных SQL Azure версии 12. При обнаружении проблем совместимости некоторые проблемы можно устранить прямо в этом инструменте. Этот инструмент может найти несовместимости, которые не обязательно исправлять. Это был первый доступный инструмент для упрощения переноса в Базу данных SQL Azure, и он активно поддерживается сообществом SQL Server. Кроме того, прямо из него можно выполнить миграцию. 

## <a name="fix-database-migration-compatibility-issues"></a>Устранение проблем совместимости при миграции базы данных
При обнаружении проблем совместимости необходимо устранить их перед выполнением миграции базы данных SQL Server. Существует широкий спектр проблем совместимости, которые могут возникнуть в зависимости от версии исходной базы данных SQL Server и сложности базы данных, для которой выполняется миграция. В более старых версиях SQL Server имеются дополнительные проблемы совместимости. Воспользуйтесь поиском в Интернете, а также следующими ресурсами:

* [Функции базы данных SQL Server, которые не поддерживаются в базе данных SQL Azure](sql-database-transact-sql-information.md)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Помимо поиска в Интернете и использования этих ресурсов можно воспользоваться [форумами сообщества SQL Server на сайте MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) или [StackOverflow](http://stackoverflow.com/).

Для устранения обнаруженных проблем используйте одно из следующих средств миграции баз данных:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* [SQL Server Data Tools для Visual Studio (SSDT)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): чтобы использовать SSDT, следует импортировать схему базы данных в SQL Server Data Tools для Visual Studio (SSDT) и выполнить сборку проекта для развертывания Базы данных SQL версии 12. Затем следует устранить все проблемы совместимости, обнаруженные в SSDT. По завершении следует синхронизировать изменения с базой данных-источником (или ее копией). В настоящее время для проверки и устранения проблем совместимости с Базой данных SQL версии 12 рекомендуется использовать именно SSDT. По следующей ссылке доступно [пошаговое руководство по SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
* Используйте [SQL Server Management Studio (SSMS)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md). Для этого исправьте ошибки, обнаруженные с помощью другого инструмента, выполняя команды Transact-SQL. Этот метод предназначен прежде всего для опытных пользователей, поскольку он предполагает изменение схемы базы данных непосредственно в исходной базе данных. 
* [Мастер миграции SQL Azure (SAMW)](sql-database-cloud-migrate-fix-compatibility-issues.md): чтобы использовать SAMW, следует создать сценарий Transact-SQL на основе базы данных-источника. Мастер преобразует этот сценарий, если это возможно, чтобы сделать схему совместимой с Базой данных SQL версии 12. После окончания процесса SAMW может подключиться к базе данных SQL версии 12 для выполнения скрипта. Этот инструмент также анализирует файлы трассировки, чтобы выявить проблемы совместимости. Сценарии могут создаваться только со схемой или могут содержать данные в формате BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Перенос совместимой базы данных SQL Server в Базу данных SQL
Корпорация Майкрософт предоставляет несколько методов переноса совместимой базы данных SQL Server для различных сценариев. Выбор способа зависит от допустимости простоев, размера и сложности базы данных SQL Server и вашего подключения к облаку Microsoft Azure.  

> [!div class="op_single_selector"]
> * [Мастер миграции SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Экспорт в BACPAC-файл](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Импорт из BACPAC-файла](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Репликация транзакций](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Первый вопрос, который нужно задать при выборе метода переноса, — можно ли вывести базу данных из эксплуатации на время переноса. Миграция базы данных при наличии активных транзакций может привести к несогласованности баз данных и повреждению базы данных. Существует множество методов замораживания базы данных — от запрета подключения клиента до создания [моментального снимка базы данных](https://msdn.microsoft.com/library/ms175876.aspx).

Для переноса с минимальным временем простоя используйте [репликацию транзакций SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) , если база данных соответствует требованиям для репликации транзакций. Если вы можете позволить себе некоторое время простоя или выполняете тестовую миграцию рабочей базы данных для последующей полноценной миграции, воспользуйтесь одним из следующих трех методов.

* [Мастер миграции SSMS.](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) Для переноса малых и средних совместимых баз данных SQL Server 2005 или более поздних версий достаточно запустить [мастер по развертыванию базы данных в базе данных Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) в среде SQL Server Management Studio.
* [Экспорт в файл BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) и последующий [импорт из файла BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md). При наличии проблем подключения (отсутствие подключения, низкая пропускная способность или проблемы с временем ожидания), а также для средних и больших баз данных используйте файл [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). При использовании этого метода схема SQL Server и данные экспортируются в BACPAC-файл. Затем этот BACPAC-файл импортируется в базу данных SQL с использованием мастера экспорта приложений уровня данных в SQL Server Management Studio или программы командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
* Совместное использование BACPAC- и BCP-файлов. Используйте [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл и [BCP](https://msdn.microsoft.com/library/ms162802.aspx)-файл для больших баз данных, чтобы увеличить распараллеливание и повысить производительность, пусть и за счет увеличения сложности. С помощью этого метода выполните перенос схемы и данных отдельно.
  
  * [Экспортируйте только схему в файл BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
  * [Импортируйте только схему из файла BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) в Базу данных SQL.
  * Используйте [BCP](https://msdn.microsoft.com/library/ms162802.aspx) для извлечения данных в плоские файлы, а затем выполните [параллельную загрузку](https://technet.microsoft.com/library/dd425070.aspx) этих файлов в базу данных SQL Azure.
    
     ![Миграция базы данных SQL Server — миграция базы данных SQL в облако](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [,](sql-database-features.md)
  [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


