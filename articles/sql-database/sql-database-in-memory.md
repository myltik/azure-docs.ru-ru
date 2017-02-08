---
title: "Технологии SQL In-Memory | Документация Майкрософт"
description: "Технологии SQL In-Memory значительно повышают производительность транзакций и аналитических операций. Узнайте, как воспользоваться преимуществами этих технологий."
services: sql-database
documentationCenter: 
authors: jodebrui
manager: jhubbard
editor: 
ms.assetid: 250ef341-90e5-492f-b075-b4750d237c05
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jodebrui
translationtype: Human Translation
ms.sourcegitcommit: c3a0976b342e1351d166e294586af988e18ce009
ms.openlocfilehash: 31ac054ec82b4ba66eee1e81fcf5f0cfeaf92e43

---

# <a name="optimize-performance-using-in-memory-technologies-in-sql-database"></a>Оптимизация производительности с помощью технологий In-Memory для базы данных SQL

Технологии выполнения в памяти (In-Memory) в Базе данных SQL Azure помогают повысить производительность транзакционных (OLTP), аналитических (OLAP) и смешанных (HTAP) рабочих нагрузок. Благодаря высокой эффективности обработки запросов и транзакций технологии In-Memory позволяют также снизить затраты. Вам не нужно переходить на более высокую ценовую категорию баз данных, чтобы добиться повышения производительности. В некоторых случаях технологии In-Memory предусматривают даже переход на более низкую ценовую категорию без снижения производительности. 

- Используя выполняющуюся в памяти OLTP, [компания Quorum Business Solutions смогла вдвое увеличить свои рабочие нагрузки, при этом повысив показатели DTU (т. е. потребление ресурсов) на 70%](https://customers.microsoft.com/en-US/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database). 
- В следующем видеоролике на примере рабочей нагрузки показано, как достигается значительное улучшение показателей потребления ресурсов: [In-Memory OLTP in Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (Выполняющаяся в памяти OLTP в Базе данных SQL Azure).

Технологии In-Memory доступны для всех баз данных уровня "Премиум", в том числе для пулов эластичных баз данных уровня "Премиум". 

В следующем видео демонстрируется потенциал роста производительности при использовании технологий In-Memory для базы данных SQL Azure. Помните, что фактическое увеличение производительности всегда зависит от многих факторов, включая характер рабочей нагрузки и свойства данных, схему доступа к базе данных и т. д.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Database-In-Memory-Technologies/player]
> 
> 

База данных SQL Azure поддерживает следующие технологии In-Memory.

- *In-Memory OLTP* повышает пропускную способность и сокращает задержки при обработке транзакций. In-Memory OLTP полезно использовать в следующих сценариях: обработка транзакций с высокой пропускной способностью, например для торговли и игр, прием данных о событиях или устройствах Интернета вещей, кэширование, загрузка данных, использование временных таблиц и табличных переменных.
- *Кластеризованные индексы columnstore* снижают требования к вместимости хранилища (иногда до 10 раз) и повышают производительность запросов для отчетов и аналитики. Используйте их с таблицами фактов в киосках данных, чтобы разместить в базе больше данных и повысить ее производительность. Используйте их с данными журналов в рабочей базе данных, чтобы сохранять и использовать в 10 раз больше данных.
- *Некластеризованные индексы сolumnstore* для гибридных сценариев транзакционной и аналитической обработки (HTAP): получайте информацию о параметрах бизнеса в режиме реального времени, напрямую опрашивая рабочую базу данных без ресурсоемких операций извлечения, преобразования и загрузки, а также без задержек при заполнении хранилища данных. Некластеризованные индексы сolumnstore позволяют очень быстро выполнять аналитические запросы к базе данных OLTP, не мешая при этом рабочей нагрузке.
- Технологии In-Memory OLTP и индексы сolumnstore можно использовать вместе. Это позволит ускорить как обработку транзакций, так и выполнение аналитических запросов к тем же данным.

Индексы сolumnstore и технология In-Memory OLTP включены в выпуски SQL Server, начиная с версий 2012 и 2014 соответственно. Технология In-Memory сходным образом реализованы в базе данных SQL Azure и SQL Server. При этом новые возможности сначала предоставляются для базы данных SQL Azure, а затем переносятся в очередной выпуск SQL Server. 

В этой статье описываются особенности технологии In-Memory OLTP и индексов сolumnstore, которые применимы только к базе данных SQL Azure. Описание дополняется примерами. Сначала вы узнаете, как эти технологии влияют на использование хранилища и какие есть ограничения на размер данных. Затем вы узнаете, как правильно изменять ценовую категорию для баз данных, в которых используются эти технологии. Наконец вы ознакомитесь с двумя примерами по использованию технологии In-Memory OLTP и индексов сolumnstore в базе данных SQL Azure.

Подробные сведения об описываемых технологиях вы найдете по следующим ссылкам:

- [Общие сведения и сценарии использования](https://msdn.microsoft.com/library/mt774593.aspx) — общие сведения и сценарии использования выполняющейся в памяти OLTP. Статья также содержит ссылки на примеры реальных клиентов и сведения, необходимые для начала работы.
- [In-Memory OLTP (оптимизация в памяти)](http://msdn.microsoft.com/library/dn133186.aspx)
- [Описание индексов columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Гибридные сценарии транзакционной и аналитической обработки, которые также называются [операционной аналитикой в реальном времени](https://msdn.microsoft.com/library/dn817827.aspx)

Краткое руководство по In-Memory OLTP можно найти здесь:

- [Quick Start 1: In-Memory OLTP Technologies for Faster Transact-SQL Performance](http://msdn.microsoft.com/library/mt694156.aspx) — еще одна статья, которая поможет вам приступить к работе.

Подробные видеоролики об этих технологиях:

- [In-Memory OLTP in Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (Выполняющаяся в памяти OLTP в Базе данных SQL Azure), где демонстрируется повышение показателей производительности и описываются шаги для достижения таких же результатов.
- [In-Memory OLTP Videos: What it is and When/How to use it](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/) (Видео про технологию In-Memory OLTP: что это такое, когда и как ее использовать).
- [Columnstore Index: In-Memory Analytics (i.e. columnstore index) Videos from Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/) (Видео о выполняющейся в памяти аналитике (например, индексах columnstore) с конференции Ignite 2016).

## <a name="storage-and-data-size"></a>Емкость хранилища и объем данных

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Ограничения на объем данных и емкость хранилища для In-Memory OLTP

In-Memory OLTP включает оптимизированные для памяти таблицы, которые используются для хранения пользовательских данных. Эти таблицы должны умещаться в памяти. Так как вы можете напрямую управлять памятью в службе базы данных SQL, мы применяем концепцию квоты на пользовательские данные или *хранилище In-Memory OLTP*.

Каждая поддерживаемая ценовая категория отдельных баз данных и пулов эластичных баз данных включает некоторый объем хранилища In-Memory OLTP. На момент написания статьи мы выделяли по 1 ГБ хранилища на каждые 125 DTU или eDTU.

В статье [Уровни служб базы данных SQL для отдельных баз данных и пулов эластичных баз данных](sql-database-service-tiers.md) содержится официальный список объемов хранилища In-Memory OLTP, доступных для каждой поддерживаемой ценовой категории отдельных баз данных и пулов эластичных баз данных.

При расчете емкости хранилища In-Memory OLTP используются следующие параметры.

- Активные строки пользовательских данных в оптимизированных для памяти таблицах и переменных таблиц. Обратите внимание, что старые версии строк не учитываются в объеме.
- Индексы оптимизированных для памяти таблиц.
- Операционные затраты на операции ALTER TABLE.

Когда вы достигнете установленного ограничения, появится сообщение об израсходовании квоты, и вы не сможете больше выполнять операции вставки и обновления данных. В этом случае нужно удалить часть данных или перейти на более высокую ценовую категорию базы данных или пула.


Дополнительные сведения о мониторинге использования хранилища In-Memory OLTP и о настройке предупреждений при приближении к установленным ограничениям см. здесь:

- [Мониторинг хранилища In-Memory OLTP](sql-database-in-memory-oltp-monitoring.md)

#### <a name="note-about-elastic-pools"></a>Замечания о пулах эластичных баз данных

При использовании пулов эластичных баз данных хранилище In-Memory OLTP используется совместно всеми базами данными в пуле. Следовательно, используемый объем одной базы данных может повлиять на другие базы данных. Есть два пути устранения таких проблем.

- Установите для баз данных параметр Max-eDTU ниже, чем значение eDTU для пула в целом. Это ограничит использование хранилища In-Memory OLTP для любой отдельной базы данных пула в соответствии с указанным значением eDTU.
- Значение Min-eDTU должно быть выше 0. Это гарантирует, что каждая база данных в пуле получит некоторый объем хранилища In-Memory OLTP в соответствии с указанным значением Min-eDTU.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Размер данных и хранилище для индексов сolumnstore

Индексы сolumnStore не обязательно должны помещаться в памяти. Поэтому для размера индексов применяется только ограничение на максимальный общий размер базы данных, который описан в статье [Уровни служб базы данных SQL для отдельных баз данных и пулов эластичных баз данных](sql-database-service-tiers.md).

При использовании кластеризованных индексов сolumnstore для хранения базовых таблиц применяется сжатие по столбцам. Это может значительно снизить объем хранимых пользовательских данных, позволяя разместить в базе данных больше информации. Этот эффект можно усилить, используя [архивное сжатие по столбцам](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Степень сжатия, которой можно добиться, зависит от характера данных. Вполне достижимо 10-кратное сжатие.

Например, если для базы данных установлен максимальный размер 1 терабайт (ТБ), а с помощью технологии columnstore удастся добиться 10-кратного сжатия, вы сможете хранить в этой базе данных 10 ТБ пользовательских данных.

При использовании некластеризованных индексов сolumnstore базовая таблица хранится в традиционном формате rowstore, поэтому экономия места в хранилище будет не столь значительной, как с применением кластеризованных индексов сolumnstore. Но если вы при этом замените некоторое число традиционных некластеризованных индексов одним индексом columnstore, даже так вы заметите некоторое сокращение общего объема хранилища для этой таблицы.

## <a name="moving-databases-using-in-memory-technologies-between-pricing-tiers"></a>Изменение ценовой категории баз данных, использующих технологии In-Memory

Переход на более высокую ценовую категорию базы данных, в которой используются технологии In-Memory, не требует особых рекомендаций. Чем выше категория, тем больше функциональных возможностей и дополнительных ресурсов в нее включено. Понижение ценовой категории может повлиять на базу данных, особенно при переходе с уровня "Премиум" на уровень "Стандартный" или "Базовый", или если уровень снижается для базы данных категории "Премиум" с поддержкой In-Memory OLTP. Эти же рекомендации будут применимы при изменении на более низкую ценовой категории для пула эластичных баз данных либо при переносе баз данных с поддержкой In-Memory в пул эластичных баз данных уровня "Стандартный" или "Базовый".

### <a name="in-memory-oltp"></a>In-Memory OLTP;

*Понижение до уровня "Базовый" или "Стандартный".* In-Memory OLTP не поддерживается базами данных уровня "Стандартный" или "Базовый". Кроме того, невозможно изменить уровень базы данных, содержащей любые объекты In-Memory OLTP, на "Стандартный" или "Базовый".

- Прежде чем понижать категорию базы данных до уровня "Стандартный" или "Базовый", удалите все оптимизированные для памяти таблицы и типы таблиц, а также все модули T-SQL, скомпилированные в собственном коде.

Вы можете программным методом узнать, поддерживает ли база данных In-Memory OLTP. Для этого выполните следующий запрос Transact-SQL:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Если запрос возвращает **1**, база данных поддерживает In-Memory OLTP.


*Переход на более низкий уровень в рамках категории "Премиум".* Данные в оптимизированных для памяти таблицах должны умещаться в хранилище In-Memory OLTP, которое связано с ценовой категорией базы данных или доступно для использования в пуле эластичных баз данных. Если вы пытаетесь понизить ценовую категорию базы данных или переместить базу данных в пул, в котором недостаточно места для хранилища In-Memory OLTP, операция завершится ошибкой.

### <a name="columnstore-indexes"></a>Индексы columnstore

*Понижение до уровня "Базовый" или "Стандартный".* Индексы сolumnstore не поддерживаются базами данных уровней "Стандартный" или "Базовый". Когда ценовая категория базы данных понижается до уровня "Стандартный" или "Базовый", индексы сolumnstore становятся недоступными. Если вы используете кластеризованный индекс сolumnstore, вся таблица становится недоступной.

- Прежде чем понижать базу данных до уровня "Стандартный" или "Базовый", удалите все кластеризованные индексы сolumnstore.

*Переход на более низкий уровень в рамках категории "Премиум".* Этот сценарий будет успешным, только если общий размер базы данных не превышает максимальный размер, установленный для баз данных в новой ценовой категории или он соответствует доступному объему хранилища в пуле эластичных баз данных. Сами индексы сolumnstore эта процедура не затрагивает.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>О. Установка образца In-Memory OLTP

На [портале Azure](https://portal.azure.com/) вы можете быстро и просто создать пример базы данных AdventureWorksLT [вер. 12]. Также в этом разделе объясняется, как можно расширить базу данных AdventureWorksLT с использованием объектов In-Memory OLTP, чтобы реализовать повышение производительности.

Более простая, но одновременно и более наглядная демонстрация In-Memory OLTP представлена здесь:

- Выпуск: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Исходный код: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Шаги установки

1. На [портале Azure](https://portal.azure.com/)создайте базу данных уровня "Премиум" на сервере версии 12. Укажите **источник** для примера базы данных AdventureWorksLT [V12].
 - Подробные инструкции см. в статье [Создание первой базы данных Azure SQL](sql-database-get-started.md).

2. Подключитесь к базе данных с помощью SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Скопируйте в буфер обмена [скрипт Transact-SQL для In-Memory OLTP](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) .
 - Этот скрипт создаст необходимые объекты In-Memory в образце базы данных AdventureWorksLT, созданной на этапе 1.

4. Вставьте сценарий T-SQL в SSMS, а затем выполните его.
 - Как и в блоке ниже, ключевым является предложение `MEMORY_OPTIMIZED = ON` инструкции CREATE TABLE:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Ошибка 40536


При запуске скрипта T-SQL может появиться ошибка 40536. В таком случае выполните следующий скрипт T-SQL, чтобы проверить, поддерживает ли база данных компонент In-Memory:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Значение **0** указывает на то, что In-Memory не поддерживается, а значение 1 — что поддерживается. Чтобы диагностировать проблему, выполните следующие действия:

- Убедитесь, что для базы данных выбран уровень служб «Премиум».


#### <a name="about-the-created-memory-optimized-items"></a>Сведения о созданных элементах, оптимизированных для памяти

**Таблицы**— пример содержит следующие оптимизированные для памяти таблицы:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


В SSMS-файле оптимизированные для памяти таблицы можно проверить с помощью **обозревателя объектов** следующим образом.

- Щелкните правой кнопкой мыши **Таблицы** > **Фильтры** > **Параметры фильтров** > **Оптимизация для памяти** и задайте значение 1.


Или можно отправить запрос представлений каталога:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Скомпилированная в собственном коде хранимая процедура** — процедуру SalesLT.usp_InsertSalesOrder_inmem тоже можно проверить с помощью запроса представления каталога.


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Запуск образца рабочей нагрузки OLTP

Единственное различие между двумя следующими *хранимыми процедурами* состоит в том, что первая процедура использует оптимизированные для памяти версии таблиц, а вторая — обычные таблицы на диске:

- SalesLT**.**usp_InsertSalesOrder**_inmem**
- SalesLT**.**usp_InsertSalesOrder**_ondisk**


В этом разделе описано, как с помощью удобной служебной программы **ostress.exe** можно выполнить две хранимые процедуры в режиме нагрузочного теста. При этом вы можете сравнить время выполнения этих нагрузочных тестов.


При запуске программы ostress.exe рекомендуется передавать значения параметров:

- -n100 — для выполнения большого количества одновременных подключений;
- -r500 — для многократного (сотни раз) выполнения каждого цикла подключения.


Тем не менее проверку работоспособности можно выполнить и с помощью гораздо меньших значений, например -n10 и -r50.


### <a name="script-for-ostressexe"></a>Скрипт для ostress.exe


В этом разделе приведен скрипт T-SQL, внедренный в командную строку ostress.exe. Этот скрипт использует элементы, созданные ранее с помощью установленного скрипта T-SQL.


Следующий скрипт вставляет образец заказа на продажу с пятью позициями строки в следующие оптимизированные для памяти *таблицы*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Чтобы создать версию представленного выше скрипта T-SQL для ostress.exe на диске (*_ondisk*), просто измените оба вхождения подстроки *_inmem* на *_ondisk*. Эти замены влияют на имена таблиц и хранимых процедур.


### <a name="install-rml-utilities-and-ostress"></a>Установка служебных программ RML и ostress


В идеале вам следует запланировать запуск ostress.exe на виртуальной машине Azure. Вам нужно создать [виртуальную машину Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) в географическом регионе Azure, в котором находится база данных AdventureWorksLT. Но вы также можете запустить программу ostress.exe и на переносном компьютере.


На виртуальной машине (или в другом размещении) установите служебные программы RML, которые включают ostress.exe.

- Обсуждение программы ostress.exe представлено в статье [Пример базы данных для выполняющейся в памяти OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Или ознакомьтесь со статьей [Пример базы данных для выполняющейся в памяти OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Можно также прочитать [запись блога об установке ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Запуск тестовой рабочей нагрузки *_inmem*


Вы можете использовать окно *командной строки RML* , чтобы запустить командную строку ostress.exe. Параметры командной строки указывают программе ostress выполнять следующие действия:

- параллельно выполнять 100 подключений (-n100);
- заставлять каждое подключение запускать сценарий T-SQL 50 раз (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Выполнить предыдущую команду ostress.exe можно так.


1. Чтобы удалить все данные, вставленные в ходе предыдущих запусков, сбросьте содержимое базы данных, выполнив следующую команду в SSMS.
```
EXECUTE Demo.usp_DemoReset;
```

2. Скопируйте текст предыдущей командной строки ostress.exe в буфер обмена.

3. Замените `<placeholders>` для параметров -S, -U, -P, и -d правильными фактическими значениями.

4. В окне командной строки RML запустите измененную командную строку.


#### <a name="result-is-a-duration"></a>Результат — это длительность выполнения теста


При завершении программа ostress.exe записывает значение длительности выполнения в качестве последней строки выходных данных в окне командной строки RML. Например, более короткий тестовый запуск длится около 1,5 минут:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Сброс базы данных, изменение значения *_ondisk* и повторный запуск


Получив результат выполнения *_inmem*, выполните следующие действия для запуска *_ondisk*:


1. Выполните сброс базы данных, запустив следующую команду в SSMS. Она удалит все данные, вставленные в ходе предыдущего запуска.
```
EXECUTE Demo.usp_DemoReset;
```

2. Измените командную строку ostress.exe, заменив все вхождения *_inmem* на *_ondisk*.

3. Перезапустите ostress.exe еще раз и запишите результат (длительность выполнения).

4. Еще раз выполните сброс базы данных, чтобы корректно удалить большой объем тестовых данных.


#### <a name="expected-comparison-results"></a>Ожидаемые результаты сравнения

Наши выполняющиеся в памяти тесты демонстрируют **9-кратное** повышение производительности для упрощенных рабочих нагрузок. При этом программа ostress выполняется на виртуальной машине Azure, расположенной в том же регионе Azure, что и база данных.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Установка образца In-Memory Analytics


В этом разделе вы сравните результаты ввода-вывода и статистические данные при использовании индекса columnstore и традиционного индекса сбалансированного дерева.


Для анализа в режиме реального времени с использованием рабочей нагрузки OLTP зачастую лучше использовать НЕкластеризованный индекс columnstore. Дополнительные сведения см. в статье [Описание индексов columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Подготовка тестирования аналитики с помощью columnstore


1. С помощью портала Azure создайте из образца новую базу данных AdventureWorksLT.
 - Используйте такое же имя.
 - Выберите любой уровень служб категории «Премиум».

2. Скопируйте [sql_inmemory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) в буфер обмена.
 - Этот скрипт создаст необходимые объекты In-Memory в образце базы данных AdventureWorksLT, созданной на этапе 1.
 - Скрипт создает таблицу измерений и две таблицы фактов. Таблицы фактов заполняются 3,5 млн строк.
 - Выполнение скрипта может занять до 15 минут.

3. Вставьте сценарий T-SQL в SSMS, а затем выполните его.
 - Важным является ключевое слово **COLUMNSTORE** в инструкции **CREATE INDEX**, как и в:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Задайте для базы данных AdventureWorksLT уровень совместимости 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Уровень 130 не имеет прямого отношения к компонентам In-Memory. При этом уровень 130 обычно обеспечивает более высокую скорость обработки запросов, чем уровень 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Ключевые таблицы и индексы columnstore


- dbo.FactResellerSalesXL_CCI — это таблица, включающая в себя кластеризованный индекс **columnstore** с дополнительными возможностями сжатия на уровне *данных*.

- dbo.FactResellerSalesXL_PageCompressed — это таблица с эквивалентным обычным кластеризованным индексом, который сжат только на уровне *страницы*.


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Критические запросы для сравнения с индексом columnstore


[Здесь](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) представлено несколько типов запросов T-SQL, которые можно выполнить для анализа повышения производительности. На этапе 2 в сценарии T-SQL описано два интересующих нас запроса. Эти запросы отличаются только одной строкой:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Кластеризованный индекс columnstore включен в таблицу FactResellerSalesXL\_CCI.

Следующий фрагмент скрипта T-SQL отображает статистику для ввода-вывода, а также время выполнения запроса (TIME) для каждой таблицы.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

Для этого запроса в базе данных ценовой категории P2 можно ожидать роста производительности примерно в 9 раз при использовании кластеризованного индекса по сравнению с традиционным индексом. Для P15 можно ожидать роста производительности при использовании технологии columnstore примерно в 57 раз.



## <a name="next-steps"></a>Дальнейшие действия

- [Краткое руководство 1. Технологии выполнения OLTP в памяти для повышения производительности службы Transact-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [Используйте In-Memory OLTP в существующем приложении SQL Azure.](sql-database-in-memory-oltp-migration.md)

- [Мониторинг хранилища OLTP в памяти](sql-database-in-memory-oltp-monitoring.md) описывает In-Memory OLTP.


## <a name="additional-resources"></a>Дополнительные ресурсы

#### <a name="deeper-information"></a>Подробные сведения

- Узнайте, как [Quorum удваивает ключевую рабочую нагрузку на базу данных при одновременном сокращении DTU на 70 % благодаря использованию In-Memory OLTP для базы данных SQL](https://customers.microsoft.com/en-US/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Learn about In-Memory OLTP](http://msdn.microsoft.com/library/dn133186.aspx) (Знакомство с In-Memory OLTP)

- [Learn about Columnstore Indexes](https://msdn.microsoft.com/library/gg492088.aspx) (Сведения об индексах Columnstore)

- [Learn about Real-Time Operational Analytics](http://msdn.microsoft.com/library/dn817827.aspx) (Сведения об операционной аналитике в реальном времени)

- Технический документ с [рекомендациями по распространенным шаблонам рабочих нагрузок и миграции](http://msdn.microsoft.com/library/dn673538.aspx)включает описание шаблонов рабочих нагрузок, для которых In-Memory OLTP обычно обеспечивает значительное повышение производительности.

#### <a name="application-design"></a>Проектирование приложений

- [In-Memory OLTP (оптимизация в памяти)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Используйте In-Memory OLTP в существующем приложении SQL Azure.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Средства

- [портале Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)



<!--HONumber=Dec16_HO2-->


