---
title: Технологии обработки в оперативной памяти в базе данных SQL Azure | Документация Майкрософт
description: Технологии обработки в оперативной памяти в базе данных SQL Azure значительно повышают производительность транзакций и аналитических операций.
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 04/04/2018
ms.author: jodebrui
ms.openlocfilehash: b4f8388fdf104253aad07de77e89c30df4e4b128
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195174"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Оптимизация производительности с помощью технологий обработки в оперативной памяти в базе данных SQL

С помощью технологий обработки в оперативной памяти, доступных в базе данных SQL Azure, можно добиться улучшения производительности при различных рабочих нагрузках: транзакции (интерактивная обработка транзакций (OLTP)), аналитические операции (интерактивная аналитическая обработка (OLAP)) и нагрузки смешанного типа (гибридная транзакционная и аналитическая обработка (HTAP)). Благодаря более эффективной обработке запросов и транзакций технологии обработки в оперативной памяти также помогут снизить затраты. Вам не нужно переходить на более высокую ценовую категорию баз данных, чтобы добиться повышения производительности. В некоторых случаях технологии обработки в оперативной памяти предусматривают даже переход на более низкую ценовую категорию без снижения производительности.

Вот несколько примеров того, как выполняющаяся в памяти OLTP помогла значительно улучшить показатели производительности:

- Используя выполняющуюся в памяти OLTP, [компания Quorum Business Solutions смогла вдвое увеличить свои рабочие нагрузки, при этом улучшив показатели DTU на 70 %](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU означает *единицу передачи данных* и содержит оценку потребления ресурсов.
- В следующем видеоролике на примере рабочей нагрузки показано, как достигается значительное улучшение показателей потребления ресурсов: [In-Memory OLTP in Azure SQL DB](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (Выполняющаяся в памяти OLTP в базе данных SQL Azure).
    - Дополнительные сведения см. в записи блога [In-Memory OLTP in Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (Выполняющаяся в памяти OLTP в базе данных SQL Azure).

Технологии обработки в оперативной памяти доступны для всех баз данных уровня "Премиум", в том числе для пулов эластичных баз данных уровня "Премиум".

В следующем видео демонстрируется потенциал роста производительности при использовании технологий In-Memory для базы данных SQL Azure. Помните, что фактическое увеличение производительности всегда зависит от многих факторов, включая характер рабочей нагрузки и свойства данных, схему доступа к базе данных и т. д.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

База данных SQL Azure поддерживает следующие технологии In-Memory.

- *Выполняющаяся в памяти OLTP* повышает производительность транзакций и сокращает задержки при их обработке. Выполняющуюся в памяти OLTP полезно использовать в следующих сценариях: обработка транзакций с высокой пропускной способностью, например для торговли и игр, прием данных о событиях или устройствах Интернета вещей, кэширование, загрузка данных, использование временных таблиц и табличных переменных.
- *Кластеризованные индексы columnstore* снижают требования к вместимости хранилища (иногда до 10 раз) и повышают производительность запросов для отчетов и аналитики. Используйте их с таблицами фактов в киосках данных, чтобы разместить в базе больше данных и повысить ее производительность. Вы также можете использовать их с данными журналов в рабочей базе данных, чтобы архивировать и запрашивать в 10 раз больше данных.
- *Некластеризованные индексы columnstore* для гибридных сценариев транзакционной и аналитической обработки помогут вам получить информацию о параметрах бизнеса в режиме реального времени, напрямую запрашивая рабочую базу данных без необходимости выполнения ресурсоемких операций извлечения, преобразования и загрузки, а также без задержек при заполнении хранилища данных. Некластеризованные индексы columnstore позволяют очень быстро выполнять аналитические запросы к базе данных OLTP, практически не оказывая влияния на рабочую нагрузку.
- Вы также можете сочетать таблицу, оптимизированную для обработки в памяти, с индексом columnstore. Такое сочетание позволит ускорить для одного набора данных *одновременно* и обработку транзакций, и выполнение аналитических запросов.

Индексы columnstore и выполняющаяся в памяти OLTP включены в выпуски SQL Server, начиная с версий 2012 и 2014 соответственно. Технология обработки в оперативной памяти схожим образом реализована в базе данных SQL Azure и SQL Server. При этом новые возможности сначала предоставляются для базы данных SQL Azure, а затем выпускаются для SQL Server.

В этой статье описываются особенности выполняющейся в памяти OLTP и индексов columnstore, которые применимы только к базе данных SQL Azure. Описание дополняется примерами.
- Сначала мы рассмотрим, как эти технологии влияют на использование хранилища и ограничения размера данных.
- Вы узнаете, как правильно изменять ценовую категорию для баз данных, в которых используются эти технологии.
- Вы ознакомитесь с двумя примерами по использованию выполняющейся в памяти OLTP и индексов columnstore в базе данных SQL Azure.

Для получения дополнительных сведений см. следующие ресурсы.

Ниже приведены ресурсы с более подробными сведениями относительно технологий.

- Статья [Общие сведения и сценарии использования](https://msdn.microsoft.com/library/mt774593.aspx) содержит ссылки на примеры реальных клиентов и сведения, необходимые для начала работы.
- [In-Memory OLTP (оптимизация в памяти)](http://msdn.microsoft.com/library/dn133186.aspx)
- [Описание индексов columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Гибридные сценарии транзакционной и аналитической обработки, которые также называются [операционной аналитикой в реальном времени](https://msdn.microsoft.com/library/dn817827.aspx).

Краткая ознакомительная информация относительно выполняющейся в памяти OLTP: [Краткое руководство 1. Технологии выполнения OLTP в памяти для повышения производительности службы Transact-SQL](http://msdn.microsoft.com/library/mt694156.aspx) (еще одна статья, которая поможет вам приступить к работе).

Подробные видеоролики об этих технологиях:

- Видеоролик [In-Memory in Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (Выполняющаяся в памяти OLTP в базе данных SQL Azure), в котором демонстрируется повышение показателей производительности и описываются шаги для достижения таких же результатов.
- [In-Memory OLTP Videos: What it is and When/How to use it](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/) (Видео про технологию In-Memory OLTP: что это такое, когда и как ее использовать).
- [Columnstore Index: In-Memory Analytics (i.e. columnstore index) Videos from Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/) (Видео о выполняющейся в памяти аналитике (например, индексах columnstore) с конференции Ignite 2016).

## <a name="storage-and-data-size"></a>Емкость хранилища и объем данных

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Ограничения на объем данных и емкость хранилища для In-Memory OLTP

In-Memory OLTP включает оптимизированные для памяти таблицы, которые используются для хранения пользовательских данных. Эти таблицы должны умещаться в памяти. Так как вы управляете памятью непосредственно в службе базы данных SQL, мы применяем концепцию квоты на пользовательские данные, или *хранилище выполняющейся в памяти OLTP*.

Каждая поддерживаемая ценовая категория отдельных баз данных и пулов эластичных баз данных включает некоторый объем хранилища выполняющейся в памяти OLTP. Просмотрите ограничения ресурсов на основе [DTU](sql-database-dtu-resource-limits.md) и [виртуальных ядер](sql-database-vcore-resource-limits.md).

При расчете емкости хранилища для выполняющейся в памяти OLTP используются следующие параметры.

- Активные строки пользовательских данных в оптимизированных для памяти таблицах и переменных таблиц. Обратите внимание, что старые версии строк не учитываются в объеме.
- Индексы оптимизированных для памяти таблиц.
- Операционные затраты на операции ALTER TABLE.

Когда ограничение будет достигнуто, вы получите ошибку с сообщением о том, что квота израсходована, и не сможете выполнять операции вставки и обновления данных. Чтобы устранить такую проблему, удалите часть данных или перейдите на более высокую ценовую категорию базы данных или пула.

Дополнительные сведения о мониторинге использования хранилища выполняющейся в памяти OLTP и о настройке оповещений при приближении к установленным ограничениям см. в статье [Мониторинг хранилища OLTP в памяти](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>О пулах эластичных баз данных

При использовании пулов эластичных баз данных хранилище выполняющейся в памяти OLTP используется совместно всеми базами данных в пуле. Следовательно, используемый объем одной базы данных может повлиять на другие базы данных. Есть два пути устранения таких проблем.

- Установите для баз данных значение `Max-eDTU` или `MaxvCore` ниже, чем значение eDTU или число виртуальных ядер для пула в целом. Это новое ограничение eDTU на емкость хранилища выполняющейся в памяти OLTP будет действовать в отношении любой базы данных в пуле.
- Установите для баз данных значение `Min-eDTU` или `MinvCore` больше нуля. Это минимальное значение гарантирует, что каждая база данных в пуле получит определенный объем хранилища для выполняющейся в памяти OLTP в соответствии с указанным значением `Min-eDTU` или `vCore`.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Размер данных и хранилище для индексов сolumnstore

Индексы сolumnstore не обязательно должны помещаться в памяти. Поэтому для размера индексов применяется только одно ограничение — на максимальный общий размер базы данных, который описан в статьях [Модель приобретения на основе DTU для базы данных SQL Azure](sql-database-service-tiers-dtu.md) и [Модель приобретения на основе виртуальных ядер для базы данных SQL Azure (предварительная версия)](sql-database-service-tiers-vcore.md).

При использовании кластеризованных индексов сolumnstore для хранения базовых таблиц применяется сжатие по столбцам. Сжатие может значительно снизить объем хранимых пользовательских данных, позволяя разместить в базе данных больше информации. Этот эффект можно усилить, используя [архивное сжатие по столбцам](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Степень сжатия, которой можно добиться, зависит от характера данных. Вполне можно достигнуть 10-кратного сжатия.

Например, если для базы данных установлен максимальный размер 1 терабайт (ТБ), а с помощью технологии columnstore удастся добиться 10-кратного сжатия, вы сможете хранить в этой базе данных 10 ТБ пользовательских данных.

При использовании некластеризованных индексов columnstore базовая таблица по-прежнему хранится в традиционном формате rowstore, поэтому экономия места в хранилище будет не столь значительной, как с применением кластеризованных индексов сolumnstore. Но если вы при этом замените некоторое число традиционных некластеризованных индексов одним индексом columnstore, даже так вы заметите некоторое сокращение общего объема хранилища для этой таблицы.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Изменение ценовых категорий баз данных, использующих технологии обработки в оперативной памяти

При обновлении до более высокой ценовой категории, например со "Стандартной" до "Премиум", не возникнет никаких проблем совместимости или других несоответствий. Все доступные функции и ресурсы будут только расширяться.

Но понижение ценовой категории может отрицательно повлиять на базу данных. Такое влияние при переходе с категории "Премиум" на "Стандартную" или "Базовую" будет особенно заметно, если база данных содержит объекты выполняющейся в памяти OLTP. После понижения ценовой категории или уровня оптимизированные для памяти таблицы станут недоступными (даже если останутся видимыми). Эти замечания относятся и к переходу на более низкую ценовую категорию для пула эластичных баз данных, а также к перемещению баз данных, использующих технологии обработки в оперативной памяти, в пул эластичных баз данных уровня "Стандартный" или "Базовый".

### <a name="in-memory-oltp"></a>In-Memory OLTP;

*Переход на уровни "Базовый" и "Стандартный"*: выполняющаяся в памяти OLTP не поддерживается базами данных уровней "Стандартный" или "Базовый". Кроме того, невозможно изменить уровень базы данных, содержащей любые объекты выполняющейся в памяти OLTP, на "Стандартный" или "Базовый".

Вы можете программным методом узнать, поддерживает ли база данных In-Memory OLTP. Для этого выполните следующий запрос Transact-SQL:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Если запрос возвращает **1**, база данных поддерживает In-Memory OLTP.

Прежде чем понижать базу данных до уровней "Стандартный" или "Базовый", удалите все оптимизированные для памяти таблицы и типы таблиц, а также все модули T-SQL, скомпилированные в собственном коде. Приведенные ниже запросы определяют все объекты, которые необходимо удалить перед переходом на ценовую категорию "Стандартный" или "Базовый".

```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

*Понижение до более низкого уровня "Премиум"*: данные в оптимизированных для памяти таблицах должны умещаться в хранилище выполняющейся в памяти OLTP, которое связано с ценовой категорией базы данных или доступно для использования в пуле эластичных баз данных. Если вы попытаетесь понизить ценовую категорию базы данных или переместить базу данных в пул, в котором нет достаточного объема хранилища выполняющейся в памяти OLTP, операция завершится ошибкой.

### <a name="columnstore-indexes"></a>Индексы columnstore

*Понижение до ценовой категории "Стандартный" или "Базовый".* Индексы columnstore поддерживаются только в ценовой категории "Премиум" и "Стандартный" (S3 и выше), но не в ценовой категории "Базовый". При переходе базы данных на неподдерживаемую ценовую категорию или уровень, индексы columnstore становятся недоступными. Система сохранит ваш индекс columnstore, но не будет его использовать. Если вы позднее перейдете на поддерживаемую ценовую категорию или уровень, индекс columnstore будет немедленно готов к использованию.

Если у вас есть **кластеризованный** индекс columnstore, после понижения вся таблица станет недоступной. Поэтому корпорация Майкрософт рекомендует удалить все *кластеризованные* индексы columnstore перед переходом базы данных на неподдерживаемую ценовую категорию или уровень.

*Понижение ценовой категории или уровня.* Такая операция пройдет успешно, если общий размер базы данных не превышает максимальный размер хранилища, предусмотренный в новой ценовой категории для баз данных, или доступный в пуле эластичных баз данных. Индексы columnstore особого влияния не оказывают.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Установка образца In-Memory OLTP

На [портале Azure](https://portal.azure.com/) вы можете быстро и просто создать пример базы данных AdventureWorksLT. Также в этом разделе объясняется, как можно расширить базу данных AdventureWorksLT с использованием объектов выполняющейся в памяти OLTP, чтобы реализовать повышение производительности.

Упрощенная, но одновременно более наглядная демонстрация выполняющейся в памяти OLTP представлена здесь:

- Выпуск: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Исходный код: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Шаги установки

1. На [портале Azure](https://portal.azure.com/) создайте на сервере базу данных уровня "Премиум" или "Критически важный для бизнеса" (предварительная версия). Укажите **источник** для примера базы данных AdventureWorksLT. Подробные инструкции см. в статье [Начало работы с серверами баз данных SQL Azure, базами данных и правилами брандмауэра с использованием портала Azure и SQL Server Management Studio](sql-database-get-started-portal.md).

2. Подключитесь к базе данных с помощью SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Скопируйте в буфер обмена [скрипт Transact-SQL для In-Memory OLTP](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) . Скрипт T-SQL создаст необходимые объекты обработки в оперативной памяти в образце базы данных AdventureWorksLT, созданной на этапе 1.

4. Вставьте сценарий T-SQL в SSMS, а затем выполните его. `MEMORY_OPTIMIZED = ON`Инструкции CREATE TABLE являются ключевыми. Например: 


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


Значение **0** указывает на то, что обработка в оперативной памяти не поддерживается, а значение **1** — наоборот. Чтобы установить причину проблемы, убедитесь, что для базы данных выбран уровень службы "Премиум".


#### <a name="about-the-created-memory-optimized-items"></a>Сведения о созданных элементах, оптимизированных для памяти

**Таблицы**— пример содержит следующие оптимизированные для памяти таблицы:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


С помощью **обозревателя объектов** в SSMS-файле можно проверить оптимизированные для памяти таблицы. Щелкните правой кнопкой мыши **Таблицы** > **Фильтры** > **Параметры фильтров** > **Оптимизирован для памяти**. Значение равно 1.


Можно также отправить запрос представлений каталога:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Скомпилированная в собственном коде хранимая процедура**: процедуру SalesLT.usp_InsertSalesOrder_inmem можно проверить с помощью запроса представления каталога.


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Запуск образца рабочей нагрузки OLTP

Единственное различие между двумя следующими *хранимыми процедурами* состоит в том, что первая процедура использует оптимизированные для памяти версии таблиц, а вторая — обычные таблицы на диске:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


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


Чтобы создать версию представленного выше скрипта T-SQL для ostress.exe для таблиц *на диске*, измените оба вхождения подстроки *_inmem* на *_ondisk*. Эти замены влияют на имена таблиц и хранимых процедур.


### <a name="install-rml-utilities-and-ostress"></a>Установка служебных программ RML и ostress


В идеале вам следует запланировать запуск ostress.exe на виртуальной машине Azure. Вам нужно создать [виртуальную машину Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) в том же географическом регионе Azure, в котором находится база данных AdventureWorksLT. Но вы также можете запустить программу ostress.exe и на переносном компьютере.


На виртуальной машине (или в другом размещении) установите служебные программы RML, которые включают в себя ostress.exe.

Дополнительные сведения можно найти в разделе 
- Обсуждение программы ostress.exe представлено в статье [Пример базы данных для выполняющейся в памяти OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
- Или ознакомьтесь со статьей [Пример базы данных для выполняющейся в памяти OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
- Можно также прочитать [блог об установке ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



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

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Скопируйте текст предыдущей командной строки ostress.exe в буфер обмена.

3. Замените `<placeholders>` для параметров -S, -U, -P, и -d правильными фактическими значениями.

4. В окне командной строки RML запустите измененную командную строку.


#### <a name="result-is-a-duration"></a>Результат — это длительность выполнения теста


При завершении программа ostress.exe записывает значение длительности выполнения в последней строке выходных данных в окне командной строки RML. Например, более короткий тестовый запуск длится около 1,5 минут:

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

Выполняющиеся в памяти тесты демонстрируют **9-кратное** повышение производительности для упрощенных рабочих нагрузок. При этом программа ostress выполняется на виртуальной машине Azure, расположенной в том же регионе Azure, что и база данных.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Установка образца In-Memory Analytics


В этом разделе вы сравните результаты ввода-вывода и статистические данные при использовании индекса columnstore и традиционного индекса сбалансированного дерева.


Для анализа в режиме реального времени с использованием рабочей нагрузки OLTP зачастую лучше использовать некластеризованный индекс columnstore. Дополнительные сведения см. в статье [Руководство по индексам columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Подготовка тестирования аналитики с помощью columnstore


1. С помощью портала Azure создайте из образца новую базу данных AdventureWorksLT.
 - Используйте такое же имя.
 - Выберите любой уровень служб категории «Премиум».

2. Скопируйте [sql_inmemory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) в буфер обмена.
 - Скрипт T-SQL создаст необходимые объекты обработки в оперативной памяти в образце базы данных AdventureWorksLT, созданной на этапе 1.
 - Скрипт создаст таблицу измерений и две таблицы фактов. Таблицы фактов заполняются 3,5 млн строк.
 - Выполнение скрипта может занять до 15 минут.

3. Вставьте сценарий T-SQL в SSMS, а затем выполните его. Важным является ключевое слово **COLUMNSTORE** в инструкции **CREATE INDEX**, как и в:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Задайте для базы данных AdventureWorksLT уровень совместимости 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Уровень 130 не имеет прямого отношения к компонентам In-Memory. При этом уровень 130 обычно обеспечивает более высокую скорость обработки запросов, чем уровень 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Ключевые таблицы и индексы columnstore


- dbo.FactResellerSalesXL_CCI — это таблица, включающая в себя кластеризованный индекс columnstore с дополнительными возможностями сжатия на уровне *данных*.

- dbo.FactResellerSalesXL_PageCompressed — это таблица с эквивалентным обычным кластеризованным индексом, который сжат только на уровне *страницы*.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Ключевые запросы для сравнения с индексом columnstore


Существует [несколько типов запросов T-SQL, которые можно выполнить](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) для анализа повышения производительности. На втором шаге скрипта T-SQL обратите внимание на следующую пару запросов. Они отличаются только одной строкой.


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


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
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

При использовании кластеризованного индекса рост производительности для этого запроса в базе данных ценовой категории P2 может увеличиться примерно в 9 раз по сравнению с традиционным индексом. При использовании кластеризованного индекса в базе данных ценовой категории P15 можно ожидать рост производительности примерно в 57 раз.



## <a name="next-steps"></a>Дополнительная информация

- [Краткое руководство 1. Технологии выполнения OLTP в памяти для повышения производительности службы Transact-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [Повышение производительности приложений в базе данных SQL с помощью выполняющейся в памяти OLTP](sql-database-in-memory-oltp-migration.md)

- [Мониторинг хранилища OLTP в памяти](sql-database-in-memory-oltp-monitoring.md)


## <a name="additional-resources"></a>Дополнительные ресурсы

#### <a name="deeper-information"></a>Подробные сведения

- Узнайте, как [Quorum удваивает ключевую рабочую нагрузку на базу данных при одновременном сокращении DTU на 70 % благодаря использованию In-Memory OLTP для базы данных SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [In-Memory OLTP in Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (Выполняющаяся в памяти OLTP в базе данных SQL Azure)

- [Learn about In-Memory OLTP](http://msdn.microsoft.com/library/dn133186.aspx) (Знакомство с In-Memory OLTP)

- [Руководство по индексам columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Начало работы с Columnstore для получения операционной аналитики в реальном времени](http://msdn.microsoft.com/library/dn817827.aspx)

- Технический документ с [рекомендациями по распространенным шаблонам рабочих нагрузок и миграции](http://msdn.microsoft.com/library/dn673538.aspx) включает описание шаблонов рабочих нагрузок, для которых выполняющаяся OLTP обычно обеспечивает значительное повышение производительности.

#### <a name="application-design"></a>Проектирование приложений

- [In-Memory OLTP (оптимизация в памяти)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Повышение производительности приложений в базе данных SQL с помощью выполняющейся в памяти OLTP](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Средства

- [портал Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
