---
title: "Руководство. Начало работы с языком U-SQL для Azure Data Lake Analytics | Документация Майкрософт"
description: "Руководство по языку U-SQL для Azure Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 96747e898b2d84cbce9251758a90941f85112dd0
ms.openlocfilehash: 7aa3844b9bdc2c5372c6e54b05296dcde6b1c05f


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Учебник. Приступая к работе с языком U-SQL для аналитики озера данных Azure
U-SQL — это язык, который объединяет преимущества SQL и выразительные возможности вашего кода для обработки данных в любом масштабе. Масштабируемые распределенные запросы U-SQL позволяют эффективно анализировать данные в таких реляционных хранилищах, как база данных SQL Azure. С помощью U-SQL вы можете обрабатывать неструктурированные данные, применяя схему к пользовательской логике чтения и вставки, а также определяемым пользователем функциям. Кроме того, U-SQL содержит расширения, которые помогают разработчику точно управлять способом выполнения в нужном масштабе. Дополнительные сведения о философии разработки, лежащей в основе U-SQL, см. в обзорной записи блога по Visual Studio, посвященной [использованию языка U-SQL для простой обработки больших данных](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

U-SQL в некоторых аспектах отличается от ANSI SQL или T-SQL. Например, такие ключевые слова, как SELECT, следует писать прописными буквами.

 Система типов и язык выражений в рамках предложений SELECT и предикатов WHERE относятся к языку C#. Это означает, что типы данных представлены типами C#, используют семантику C# NULL, а операции сравнения внутри предиката используют синтаксис C# (например, a == "foo"). Это также означает, что значения являются полными объектами .NET, поэтому вы можете использовать любой метод для работы с объектом (например, "f o o o".Split(' ')).

См. дополнительные сведения в [справочнике по языку U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

### <a name="prerequisites"></a>Предварительные требования
Ознакомьтесь с [руководством по разработке скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Завершив работу с руководством, возвращайтесь к этой статье.

В этом руководстве описано выполнение задания Azure Data Lake Analytics с помощью следующего скрипта U-SQL:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Этот сценарий не содержит действий преобразования. Он считывает данные из исходного файла SearchLog.tsv, схематизирует их, а затем записывает набор строк в файл с именем SearchLog-first-u-sql.csv.

Обратите внимание на вопросительный знак рядом с типом данных в поле **Duration**. Это означает, что поле **Duration** может иметь значение NULL.

В скрипте вы можете встретить следующие понятия и ключевые слова.

* Переменные набора строк — каждое выражение запроса, которое возвращает набор строк, может быть назначено переменной. U-SQL в скрипте использует шаблон именования переменной T-SQL (@searchlog,, например).

 >[!NOTE]
 >Обратите внимание, что назначение не активирует принудительное выполнение. Оно просто именует выражение, позволяя создавать более сложные выражения.
* EXTRACT — с помощью этого ключевого слова можно определить схему для чтения. Схема задается именем столбца и парой имен типа C# для каждого столбца. Для извлечения TSV-файлов она использует так называемое средство извлечения (например, Extractors.Tsv()). Можно разрабатывать пользовательские средства извлечения.
* OUTPUT — это ключевое слово принимает набор строк и сериализует его. Средство Outputters.Csv() записывает файл с разделителями-запятыми в указанное расположение. Можно также разработать пользовательские средства вывода.

 >[!NOTE]
 >Эти два пути являются относительными. Также можно использовать абсолютные пути. Например:    
 >     adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
 >
 >Для доступа к файлам в связанных учетных записях хранения необходимо использовать абсолютный путь.  Для файлов, хранящихся в связанной учетной записи хранения Azure, используется следующий синтаксис: wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >Контейнеры хранилища BLOB-объектов Azure с разрешениями на доступ к общедоступным большим двоичным объектам или общедоступным контейнерам сейчас не поддерживаются.

## <a name="use-scalar-variables"></a>Использование скалярных переменных
Скалярные переменные можно использовать также для упрощения обслуживания вашего сценария. Предыдущий скрипт U-SQL также можно записать так:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Преобразования наборов строк
Используйте **SELECT** для преобразования наборов строк:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

Предложение WHERE использует [логическое выражение C#](https://msdn.microsoft.com/library/6a71f45d.aspx). Для создания собственных выражений и функций можно использовать язык выражений C#. Можно даже выполнить более сложную фильтрацию, сочетая их с помощью логического объединения (and) и разделения (or).

Следующий сценарий использует метод DateTime.Parse() и объединение.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >Второй запрос выполняется на основе результатов первого набора строк, объединяя таким образом два фильтра. Можно также повторно использовать имя переменной. Имена объединяются лексически.

## <a name="aggregate-rowsets"></a>Агрегированные наборы строк
U-SQL поддерживает знакомые предложения ORDER BY, GROUP BY и агрегаты.

Следующий запрос определяет общую длительность по регионам, а затем последовательно выводит пять результатов со значениями максимальной длительности.

Наборы строк U-SQL не сохраняют свой порядок для следующего запроса. Таким образом, чтобы упорядочить выходные данные, необходимо добавить предложение ORDER BY в предложение OUTPUT.

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Предложение ORDER BY в U-SQL необходимо использовать вместе с предложением FETCH в выражении SELECT.

Предложение HAVING в U-SQL можно использовать для ограничения выходных данных группами, которые удовлетворяют условиям HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Объединение данных
U-SQL предоставляет общие операторы объединения, такие как INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN, SEMI JOIN, для объединения не только таблиц, но и любых наборов строк (даже тех, которые создаются из файлов).

Следующий сценарий объединяет searchlog с журналом впечатлений объявления и дает нам объявления для строки запроса для конкретной даты.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL поддерживает только совместимый с ANSI синтаксис объединения: предикат Rowset1 JOIN Rowset2 ON. Старый синтаксис предиката FROM Rowset1, Rowset2 WHERE _не_ поддерживается.
Предикат в JOIN должен являться объединением равенства, а не выражением. Чтобы использовать выражение, добавьте его в предыдущее предложение SELECT набора строк. Если требуется выполнить различные сравнения, его можно переместить в предложение WHERE.

## <a name="create-databases-table-valued-functions-views-and-tables"></a>Создание баз данных, возвращающих табличные значения функций, представлений и таблиц
В U-SQL данные можно использовать в контексте базы данных и схемы. Считывать или записывать файлы при этом нужно не всегда.

Каждый сценарий U-SQL выполняется с помощью базы данных по умолчанию (master) и схемы по умолчанию (dbo) в качестве контекста по умолчанию. Можно создать собственную базу данных или схему. Чтобы изменить контекст, используйте предложение USE.

### <a name="create-a-tvf"></a>Создание функции TVF
В предыдущем скрипте U-SQL мы повторно использовали EXTRACT для чтения из одного и того же исходного файла. В U-SQL функция с табличным значением (TVF) позволяет инкапсулировать данные для повторного использования в будущем.  

Следующий сценарий создает возвращающую табличное значение функцию с именем *Searchlog()* в базе данных и схеме по умолчанию:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

Следующий скрипт демонстрирует, как использовать функцию TVF, определенную в предыдущем скрипте:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Создание представлений
Если у вас есть только одно выражение запроса, которое нужно выделить, но из которого не нужно создавать параметр, можно создать представление вместо функции с табличным значением.

Следующий сценарий создает представление с именем *SearchlogView* в базе данных и схеме по умолчанию:

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

Следующий скрипт демонстрирует, как использовать определенное представление.

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>создание таблиц.
Как и в случае с таблицами реляционной базы данных, U-SQL позволяет создать таблицу с предварительно определенной схемой или же создать таблицу и определить схему из запроса, который заполняет таблицу (CREATE TABLE AS SELECT или CTAS).

Следующий скрипт создает базу данных и две таблицы:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SearchLogDb;
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    DISTRIBUTED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Запросы к таблицам
Вы можете отправлять запросы к таблицам (созданным с помощью предыдущего скрипта) так же, как вы отправляете запросы к файлам данных. Чтобы не создавать набор строк с помощью EXTRACT, теперь можно просто ссылаться на имя таблицы.

Для чтения из таблиц нужно изменить скрипт преобразования, который использовался ранее:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

 >[!NOTE]
 >Сейчас выполнять SELECT для таблицы в том же скрипте, где создается таблица, нельзя.

## <a name="conclusion"></a>Заключение
В этом руководстве рассматривается только небольшая часть U-SQL. Из-за ограниченной области его применения здесь не рассматриваются многие другие возможности U-SQL. Вот что вы можете, к примеру, делать:

* Использование CROSS APPLY для распаковки частей строк, массивов и схем в строки.
* Использование секционированных наборов данных (наборов файлов и секционированных таблиц).
* Разработка таких определяемых пользователем операторов, как средства извлечения, средства вывода, обработчики, а также определяемых пользователем агрегатов в C#.
* Использование функций окон U-SQL.
* Управление кодом U-SQL с помощью представлений, функций с табличным значением и хранимых процедур.
* Выполнение произвольного пользовательского кода на узлах обработки.
* Подключение к базам данных SQL и создание федерации запросов к ним, а также к данным U-SQL и Azure Data Lake.

## <a name="see-also"></a>Дополнительные материалы
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Использование оконных функций U-SQL для заданий в службе аналитики озера данных Azure](data-lake-analytics-use-window-functions.md)
* [Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Сообщите нам свое мнение
* [Отправить запрос на функцию](http://aka.ms/adlafeedback)
* [Получить помощь на форумах](http://aka.ms/adlaforums)
* [Отправить отзыв о U-SQL](http://aka.ms/usqldiscuss)



<!--HONumber=Dec16_HO4-->


