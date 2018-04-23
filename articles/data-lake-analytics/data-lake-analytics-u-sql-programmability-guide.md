---
title: Руководство по программированию U-SQL для Azure Data Lake | Документация Майкрософт
description: Узнайте о наборе служб в Azure Data Lake, позволяющих создать платформу больших данных на базе облака.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: saveenr
ms.openlocfilehash: 400057b5ce79cdcf6c7651462e9f497bf647e930
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="u-sql-programmability-guide"></a>Руководство по программированию U-SQL

U-SQL — это специальный язык запросов для рабочих нагрузок, обрабатывающих большие данные. U-SQL отличается использованием SQL-подобного декларативного синтаксиса, а также расширяемостью и удобством программирования, характерными для C#. В этом руководстве мы рассмотрим расширяемость и программируемость языка U-SQL на основе C#.

## <a name="requirements"></a>Требования

Скачивание и установка [средств Azure Data Lake для Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Начало работы с U-SQL  

Рассмотрим следующий скрипт U-SQL:

```
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Этот скрипт определяет два набора строк: `@a` и `@results`. Набор строк `@results` определяется из `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Типы и выражения C# в скрипте U-SQL

Выражение U-SQL — это выражение C# в сочетании с логическими операциями U-SQL, такими как `AND`, `OR`, и `NOT`. Выражения U-SQL можно использовать с инструкциями SELECT, EXTRACT, WHERE, HAVING, GROUP BY и DECLARE. Например, следующий скрипт анализирует строку как значение даты и времени (DateTime).

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Следующий фрагмент кода анализирует значение строки даты и времени (DateTime) в предложении DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Использование выражений C# для преобразования типов данных

В примере ниже описывается преобразование данных datetime с помощью выражений C#. В этом конкретном случае строковые данные о дате и времени преобразуются в стандартное значение datetime использованием формата времени полуночи (00:00:00).

```
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Использование выражений C# для получения текущей даты

Получить текущую дату можно с помощью следующего выражения C#: `DateTime.Now.ToString("M/d/yyyy")`

Ниже приведен пример использования этого выражения в скрипте.

```
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Использование сборок .NET

Модель расширяемости U-SQL реализована как возможность добавлять пользовательский код из сборок .NET. 

### <a name="register-a-net-assembly"></a>Регистрация сборки .NET

Используйте инструкцию `CREATE ASSEMBLY`, чтобы поместить сборку .NET в базу данных U-SQL. После этого скрипты U-SQL смогут применять эти сборки, используя инструкцию `REFERENCE ASSEMBLY`. 

Следующий код показывает, как зарегистрировать сборку:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Следующий код показывает, как сослаться на сборку:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Дополнительные сведения о регистрации сборки см. в [этой статье](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/).


### <a name="use-assembly-versioning"></a>Использование версий сборок
В настоящее время в U-SQL используется платформа .NET Framework 4.5. Убедитесь, что ваши сборки совместимы с этой версией среды выполнения.

Как упоминалось выше, U-SQL выполняет код в 64-разрядном формате (x64). Поэтому код нужно компилировать так, чтобы он выполнялся в среде x64. В противном случае вы получите ошибку неправильного формата, как показано выше.

Любой передаваемый в хранилище файл (библиотеки DLL, файлы ресурсов, включая другие среды выполнения, машинные сборки, файлы конфигурации и т. д.) не может быть более 400 МБ. Общий размер всех ресурсов, развернутых с помощью инструкции DEPLOY RESOURCE или с использованием ссылок на сборки и связанные файлы, не может превышать 3 ГБ.

И наконец, важно помнить, что в каждой базе данных U-SQL может быть только одна версия любой сборки. Например, если вам одновременно нужны версии 7 и 8 библиотеки NewtonSoft Json.Net, их следует зарегистрировать в разных базах данных. Кроме того, каждый скрипт может ссылаться только на одну версию библиотеки DLL для каждой сборки. В этом отношении U-SQL соблюдает семантику C# управления сборками и версиями.

## <a name="use-user-defined-functions-udf"></a>Использование определяемых пользователем функций
В языке U-SQL концепция определяемых пользователем функций обозначает подпрограммы, которые принимают параметры, выполняют действия (например, сложные вычисления) и возвращают результат этого действия в виде определенного значения. Определяемая пользователем функция может возвращать только одно скалярное значение. Основной скрипт U-SQL может вызывать такие функции, как и любую другую скалярную функцию C#.

Мы советуем инициализировать определяемые пользователем функции U-SQL как **общедоступные** (public) и **статические** (static).

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Сначала давайте рассмотрим простой пример создания определяемой пользователем функции.

В этом сценарии нужно определить финансовый период (квартал и месяц) для первого входа в систему определенного пользователя. Финансовый год для этого примера начинается в июне.

Чтобы вычислить финансовый период, мы создаем следующую функцию C#:

```
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Она вычисляет финансовый месяц и квартал и возвращает строковое значение. Для июня (первый месяц первого финансового квартала) используются значения "Q1:P1". Для июля — "Q1:P2" и т. д.

В нашем проекте U-SQL мы будем использовать базовые функции C#.

Вот как выглядит раздел кода программной части для этого примера:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Теперь мы вызовем эту функцию из основного скрипта U-SQL. Для этого нам нужно предоставить полное имя функции, включая пространство имен, в формате: "Пространство_имен.Класс.Функция(параметр)".

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Так будет выглядеть вызов в основном скрипте U-SQL:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Ниже приведен файл выходных данных с результатом выполнения скрипта.

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Это простой пример использования встроенной определяемой пользователем функции в U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Сохранение состояния между вызовами определяемой пользователем функции
Объекты, поддерживающие программирование C# в U-SQL, могут быть более сложными. Например, они могут интерактивно взаимодействовать через глобальные переменные кода программной части. Рассмотрим приведенный ниже сценарий использования.

В больших организациях пользователи могут переключаться между разными внутренними приложениями, включая Microsoft Dynamics CRM, Power BI и т. д. Клиенты могут захотеть применить анализ телеметрии для пользовательских переключений между различными приложениями, например оценить тенденции использования и т. д. Цель для бизнеса — оптимизация использования приложений. Они могут также пожелать объединить различные приложения или процедуры входа.

Чтобы достичь этой цели, мы должны определить идентификаторы сеансов и интервалы задержки между последними сеансами.

Нам нужно найти предыдущий вход и привязать этот вход ко всем сеансам, создаваемым для одного и того же приложения. Первая трудность заключается в том, что основной скрипт U-SQL не позволяет производить вычисления с функцией LAG над уже вычисленным столбцом. Вторая трудность — мы хотим сохранить определенный сеанс для всех сеансов за тот же интервал.

Чтобы решить эту проблему, мы используем глобальную переменную в разделе кода программной части `static public string globalSession;`.

Эта глобальная переменная применяется для всего набора строк во время выполнения скрипта.

Так будет выглядеть раздел кода программной части для программы U-SQL:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Как видно из этого примера, глобальная переменная `static public string globalSession;` используется внутри функции `getStampUserSession` и инициализируется заново каждый раз, когда изменяется значение параметра сеанса.

Основной скрипт U-SQL выглядит следующим образом:

```
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Функция `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` вызывается здесь во время вычисления второго набора строк в памяти. Она передает столбец `UserSessionTimestamp` и возвращает значение до изменения `UserSessionTimestamp`.

Выходной файл будет выглядеть следующим образом:

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

В этом примере мы видим более сложный сценарий использования, в котором глобальная переменная используется в разделе кода программной части для всего набора строк в памяти.

## <a name="use-user-defined-types-udt"></a>Использование определяемых пользователем типов данных
Определяемые пользователем типы или UDT — это еще одно средство, поддерживающее возможность программирования в U-SQL. Определяемый пользователем тип в U-SQL действует так же, как аналогичный тип в C#. C# — это строго типизированный язык, в котором можно использовать встроенные и пользовательские типы данных.

U-SQL не может неявно сериализировать или десериализировать произвольные пользовательские типы данных, когда пользовательский тип данных передается между вершинами в наборах строк. Поэтому пользователь должен указать явный модуль форматирования с помощью интерфейса IFormatter. Таким образом, U-SQL получит методы сериализации и десериализации для пользовательских типов данных.

> [!NOTE]
> Встроенные средства извлечения и вывода U-SQL в настоящее время не могут сериализировать или десериализировать пользовательские типы данных в файлы или из них, даже если они используют набор IFormatter. Поэтому, если вы записываете пользовательские типы данных в файл с помощью инструкции OUTPUT или считываете их с помощью средств извлечения, вам необходимо передать их в качестве массива строк или байтов. Затем необходимо явным образом вызвать код сериализации и десериализации (метод пользовательских типов данных ToString()). Но пользовательские средства извлечения и вывода могут читать и записывать пользовательские типы данных.

При попытке использования пользовательского типа в средствах EXTRACTOR и OUTPUTTER (на основе инструкции SELECT), как показано ниже,

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

мы получим такую ошибку:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Для работы с определяемым пользователем типом в средстве OUTPUTTER мы должны сериализовать данные в строку с помощью метода ToString() или создать пользовательское средство OUTPUTTER.

Сейчас определяемые пользователем типы нельзя использовать в инструкции GROUP BY. Если определяемый пользователем тип будет использован в GROUP BY, возникает следующая ошибка:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Чтобы определить пользовательский тип, сделайте следующее.

* Добавьте приведенные ниже пространства имен.

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Добавьте `Microsoft.Analytics.Interfaces` для интерфейсов определяемых пользователем типов. Кроме того, `System.IO` может потребоваться для определения интерфейса IFormatter.

* Определите определяемый пользователем тип данных с помощью атрибута SqlUserDefinedType.

**SqlUserDefinedType** указывает, что определение типа в сборке представляет определяемый пользователем тип данных для U-SQL. Свойства этого атрибута отражают физические характеристики определяемого пользователем типа данных. Этот класс не наследуется.

SqlUserDefinedType является обязательным атрибутом для определяемых пользователем типов данных.

Ниже представлен конструктор для этого класса.  

* SqlUserDefinedTypeAttribute (модуль форматирования).

* Модуль форматирования — обязательный параметр, определяющий форматирование для определяемого пользователем типа данных. Здесь нужно передать тип интерфейса `IFormatter`.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Типичные определяемые пользователем типы данных также требуют определения интерфейса IFormatter, как показано ниже.

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

Интерфейс `IFormatter` — для сериализации и десериализации графа объекта с корневым типом \<typeparamref name="T">.

\<typeparam name="T"> Это корневой тип графа объекта для сериализации и десериализации.

* **Десериализация** — процесс, обратный сериализации данных в предоставленном потоке, в ходе которого воспроизводится граф объектов.

* **Сериализация** — процесс сериализации объекта или графа объектов с использованием заданного корня в предоставленный поток.

Экземпляр `MyType` — экземпляр типа.  
Модуль записи `IColumnWriter` или чтения `IColumnReader` — базовый поток столбца.  
Контекст `ISerializationContext` — перечисление, которое определяет набор флагов, указывающих контекст источника или назначения для потока во время сериализации.

* **Intermediate** — указывает, что исходный или целевой контекст не является материализованным хранилищем.

* **Persistent** — указывает, что исходный или целевой контекст является материализованным хранилищем.

Как и обычный тип C#, определение определяемого пользователем типа данных в U-SQL может переопределять такие операторы, как +/==/!=. Также оно может содержать статические методы. Например, если мы собираемся использовать этот определяемый пользователем тип данных в качестве параметра агрегатной функции MIN U-SQL, нужно переопределить оператор <.

Ранее в этой статье мы привели пример, в котором определяли финансовый период для конкретной даты в формате `Qn:Pn (Q1:P10)`. Следующий пример показывает, как использовать определяемый пользователем тип данных для значений финансового периода.

Ниже приведен пример раздела кода программной части с определяемым пользователем типом данных и интерфейсом IFormatter.

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Определяемый тип содержит два числа: квартал и месяц. Здесь определены операторы `==/!=/>/<` и статический метод `ToString()`.

Как упоминалось выше, определяемый пользователем тип данных можно использовать в выражениях SELECT, но нельзя — в средствах OUTPUTTER и EXTRACTOR без настраиваемой сериализации. Нужно сериализовать его в строку с помощью метода `ToString()` или использовать пользовательское средство OUTPUTTER или EXTRACTOR.

Теперь давайте рассмотрим, как можно применять определяемый пользователем тип данных. В разделе кода программной части мы изменили функцию GetFiscalPeriod следующим образом:

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Как видите, он возвращает значение с типом данных FiscalPeriod.

Ниже приведен пример его дальнейшего использования в основном скрипте U-SQL. В этом примере представлено несколько способов вызова определяемого пользователем типа данных из скрипта U-SQL.

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Ниже приведен пример полного раздела кода программной части.

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Использование определяемых пользователем статистических функций
Определяемые пользователем статистические выражения — это любые функции статистической обработки, не входящие в U-SQL. Например, это может быть статистическое выражение для пользовательских математических вычислений, объединения строк, манипуляций с ними и т. д.

Определение базового класса определяемого пользователем статистического выражения представлено ниже.

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** указывает, что тип должен быть зарегистрирован как определяемая пользователем статистическая функция. Этот класс не наследуется.

Атрибут SqlUserDefinedType является **необязательным** для определяемого пользователем статистического выражения.


Базовый класс позволяет передать три абстрактных параметра — два входных и один в качестве результата. Типы данных представлены переменными, которые должны быть определены при наследовании классов:

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** — метод, который вызывается один раз для каждой группы во время вычисления. Он предоставляет процедуры инициализации для каждой группы статистической обработки.  
* **Accumulate** — метод, который выполняется один раз для каждого значения. Он выполняет основную работу алгоритма статистической обработки. Его можно использовать для обработки значений с разными типами данных, определяемыми при наследовании класса. Он может принимать два параметра разных типов данных.
* **Terminate** — метод, который выполняется один раз на каждую группу статистической обработки в конце обработки, чтобы вывести результаты по каждой группе.

Чтобы объявить правильные типы входных и выходных данных, используйте определение класса, приведенное ниже.

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1 — первый параметр для метода Accumulate.
* T2 — второй параметр для метода Accumulate.
* TResult — возвращаемый тип для метода Terminate.

Например: 

```
public class GuidAggregate : IAggregate<string, int, int>
```

или

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Использование определяемых пользователем статистических выражений в U-SQL
Чтобы применить определяемое пользователем статистическое выражение, сначала определите его в коде программной части или укажите на него ссылку из существующих библиотек DLL для программирования, как описано выше.

Затем используйте приведенный ниже синтаксис.

```
AGG<UDAGG_functionname>(param1,param2)
```

Ниже приведен пример определяемого пользователем статистического выражения.

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

И основной скрипт U-SQL.

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

В этом сценарии мы объединяем значения идентификаторов класса для определенных пользователей.

## <a name="use-user-defined-objects-udo"></a>Использование определяемых пользователем объектов
U-SQL предоставляет возможность определять пользовательские объекты, поддерживающие программирование, которые называют определяемыми пользователями объектами.

Ниже приведен список определяемых пользователем объектов в U-SQL.

* Пользовательские средства извлечения.
    * Извлечение по строкам.
    * Используется для реализации извлечения данных из пользовательских структурированных файлов.

* Пользовательские средства вывода.
    * Вывод по строкам.
    * Используется для вывода пользовательских типов данных или пользовательских форматов файлов.

* Пользовательские средства обработки.
    * Получение одной строки и возврат одной строки.
    * Используется для сокращения числа столбцов или создания новых столбцов, значения которых основаны на значениях существующего набора столбцов.

* Пользовательские средства применения.
    * Получение одной строки и возврат от 0 до n строк.
    * Использование с инструкциями OUTER и CROSS APPLY.

* Пользовательские средства объединения.
    * Объединяют наборы строк — определяемые пользователем инструкции JOIN.

* Пользовательские средства редукции.
    * Получение n строк и возврат одной строки.
    * Используется для сокращения числа строк.

Обычно определяемые пользователем объекты вызываются явным образом в скрипте U-SQL при выполнении следующих инструкций U-SQL:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> Определяемые пользователем объекты ограничены потреблением 0,5 ГБ памяти.  Это ограничение памяти не применяется для локальных выполнений.

## <a name="use-user-defined-extractors"></a>Использование пользовательских средств извлечения
U-SQL позволяет импортировать внешние данные с помощью инструкции EXTRACT. Инструкция EXTRACT может использовать встроенные средства извлечения для определяемых пользователем объектов.  

* *Extractors.Text()*: извлекает данные из текстовых файлов с разделителями в разных кодировках.

* *Extractors.Csv()*: извлекает данные из файлов данных с разделителями-запятыми (CSV) в разных кодировках.

* *Extractors.Csv()*: извлекает данные из файлов со значением с разделением знаками табуляции (TSV), в разных кодировках.

Можно также разработать пользовательское средство извлечения. Например, если при импорте данных нам нужно выполнить одно из следующих действий:

* Изменить входные данные, разделяя столбцы или изменяя отдельные значения. Для объединения строк лучше всего использовать PROCESSOR.
* Выполнить синтаксический анализ неструктурированных данных, например веб-страниц или сообщений электронной почты, или частично структурированных данных, например документов XML или JSON.
* Выполнить анализ данных в неподдерживаемых кодировках.

Чтобы определить пользовательское средство извлечения, нужно создать интерфейс `IExtractor`. Все входные параметры средства извлечения, такие как разделители столбцов и строк, кодировки, должны быть определены в конструкторе класса. Интерфейс `IExtractor` также должен содержать определение для переопределения `IEnumerable<IRow>`:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Атрибут **SqlUserDefinedExtractor** указывает, что этот тип должен быть зарегистрирован как пользовательское средство извлечения. Этот класс не наследуется.

SqlUserDefinedExtractor — это необязательный атрибут для определения пользовательского средства извлечения. Он используется для определения свойства AtomicFileProcessing:

* bool AtomicFileProcessing.   

* Значение **true** указывает, что это средство извлечения требует атомарные входные файлы (JSON, XML и другие).
* Значение **false** указывает, что это средство извлечения умеет работать с разделенными или распределенными файлами (CSV, SEQ и другие).

Основные объекты в пользовательских объектах, поддерживающих программирование, — это **input** (ввод) и **output** (вывод). Объект input используется для перечисления входных данных как `IUnstructuredReader`, объект output — для вывода данных, полученных в результате действий по извлечению.

Получить доступ к входным данным можно через `System.IO.Stream` и `System.IO.StreamReader`.

Чтобы перечислить входные столбцы, необходимо сначала разбить входной поток, используя разделитель строк.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Затем еще раз разбиваем входную строку на столбцы:

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Чтобы сохранить выходные данные, мы используем метод `output.Set`.

Необходимо понимать, что пользовательское средство извлечения только выводит столбцы и значения, определяемые выходными данными. Вызов метода Set:

```
output.Set<string>(count, part);
```

Фактический вывод данных из средства извлечения инициируется вызовом метода `yield return output.AsReadOnly();`.

Ниже приведен пример средства извлечения.

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

В этом сценарии средство извлечения восстанавливает идентификатор для столбца guid и преобразует значения столбца user в верхний регистр. Пользовательские средства извлечения могут создавать более сложные результаты, анализируя входные данные и преобразовывая их.

Ниже представлен основной скрипт U-SQL, который использует пользовательское средство извлечения.

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Использование пользовательских средств вывода
Пользовательское средство вывода — это еще один определяемый пользователем объект U-SQL, который позволяет расширить встроенные возможности U-SQL. Как и средства извлечения, существует несколько встроенных средств вывода.

* *Outputters.Text()*: записывает данные в текстовые файлы с разделителями в разных кодировках.
* *Outputters.Csv()*: записывает данные в файлы с разделителями-запятыми (CSV) в разных кодировках.
* *Outputters.Tsv()*: записывает данные в файлы со значениями с разделением знаками табуляции (TSV) в разных кодировках.

Пользовательские средства вывода позволяют записывать данные в определяемом пользователем формате. Это может потребоваться для выполнения следующих задач:

* Запись данных в частично структурированные или неструктурированные файлы.
* Запись данных в неподдерживаемых кодировках.
* Преобразование выходных данных или добавление пользовательских атрибутов.

Чтобы определить пользовательское средство вывода, нужно создать интерфейс `IOutputter`.

Ниже приведена базовая реализация класса `IOutputter`.

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Все входные параметры средства вывода, такие как разделители столбцов и строк, кодировки и т. д., должны быть определены в конструкторе класса. Интерфейс `IOutputter` также должен содержать определение для переопределения `void Output`. При необходимости для атомарной обработки файлов можно задать атрибут `[SqlUserDefinedOutputter(AtomicFileProcessing = true)`. Дополнительные сведения см. ниже.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* Метод `Output` вызывается для каждой входной строки. Он возвращает набор строк `IUnstructuredWriter output`.
* Класс Constructor используется для передачи параметров в пользовательское средство вывода.
* Метод `Close` при необходимости можно переопределить, чтобы выходить из ресурсозатратных состояний или узнавать время записи последней строки.

Атрибут **SqlUserDefinedAggregate** указывает, что этот тип должен быть зарегистрирован как пользовательское средство вывода. Этот класс не наследуется.

SqlUserDefinedOutputter — это необязательный атрибут для определения пользовательских средств вывода. Он используется для определения свойства AtomicFileProcessing.

* bool AtomicFileProcessing.   

* **true** — указывает, что это средство вывода требует атомарные выходные файлы (JSON, XML и другие).
* **false** — указывает, что это средство вывода умеет работать с разделенными или распределенными файлами (CSV, SEQ и другие).

Основные объекты, поддерживающие программирование, — это **row** (строка) и **output** (вывод). Объект **row** используется для перечисления выходных данных в интерфейсе `IRow`, а объект **output** — для отправки выходных данных в конечный файл.

Обращение к выходным данным выполняется через интерфейс `IRow`. Выходные данные передаются построчно.

Отдельные значения перечисляются с помощью вызова метода Get интерфейса IRow.

```
row.Get<string>("column_name")
```

Имена отдельных столбцов можно определить с помощью вызова метода `row.Schema`.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Такой подход позволяет создавать гибкие средства вывода для любой схемы метаданных.

Выходные данные записываются в файл с помощью `System.IO.StreamWriter`. Для параметра потока задано значение `output.BaseStrea` в структуре `IUnstructuredWriter output`.

Обратите внимание, что после каждой итерации строк необходимо очищать буфер данных для файла. Кроме того, объект `StreamWriter` должен использоваться с включенным (по умолчанию) атрибутом Disposable и с ключевым словом **using**.

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

В противном случае — после каждой итерации вызывайте явным образом метод Flush(). Это описано в примере ниже.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Установка колонтитулов для пользовательского средства вывода
Чтобы задать верхний колонтитул, используйте последовательность однократного выполнения.

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Код в первом блоке `if (isHeaderRow)` выполняется только один раз.

Для нижнего колонтитула используйте ссылку на экземпляр `System.IO.Stream` объекта `output.BaseStream`. Запишите нижний колонтитул в метод Close() интерфейса `IOutputter`.  (Дополнительные сведения см. ниже.)

Ниже приведен пример пользовательского средства вывода.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

И основной скрипт U-SQL.

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Это средство вывода для формата HTML. Оно создает HTML-файл с данными таблицы.

### <a name="call-outputter-from-u-sql-base-script"></a>Вызов средства вывода из основного скрипта U-SQL
Чтобы вызвать пользовательское средство вывода из основного скрипта U-SQL, нужно создать новый экземпляр объекта для средства вывода.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Чтобы не создавать экземпляр объекта в основном скрипте, мы можем создать оболочку функции, как было показано в примере выше.

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

В этом случае исходный вызов будет выглядеть так:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Использование пользовательских средств обработки
Пользовательское средство обработки — это тип определяемого пользователем объекта U-SQL, который позволяет обрабатывать входящие строки, применяя к ним средства программирования. Оно позволяет объединять столбцы, изменять значения или добавлять новые столбцы при необходимости. По сути оно позволяет получить нужные элементы данных, обрабатывая наборы строк.

Чтобы определить пользовательское средство обработки, нужно создать интерфейс `IProcessor` с атрибутом `SqlUserDefinedProcessor`, который является необязательным для этого средства.

Этот интерфейс должен содержать определение для переопределения набора строк интерфейса `IRow`, как показано в примере ниже.

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** указывает, что тип должен быть зарегистрирован как пользовательское средство обработки. Этот класс не наследуется.

Атрибут SqlUserDefinedProcessor является **необязательным** для определения пользовательского средства обработки.

Основные объекты, поддерживающие программирование, — это **input** (ввод) и **output** (вывод). Объект input используется для перечисления входных столбцов, а объект output — для вывода данных, полученных в результате действий по обработке.

Для перечисления входных столбцов мы используем метод `input.Get`.

```
string column_name = input.Get<string>("column_name");
```

Параметр для метода `input.Get` — это столбец, представленный в предложении `PRODUCE` для инструкции `PROCESS` в основном скрипте U-SQL. Здесь необходимо использовать правильный тип данных.

Для выходных данных используйте метод `output.Set`.

Необходимо понимать, что пользовательское средство только выводит столбцы и значения, определяемые вызовом метода `output.Set`.

```
output.Set<string>("mycolumn", mycolumn);
```

Фактический вывод данных из средства обработки инициируется вызовом метода `return output.AsReadOnly();`.

Ниже приведен пример использования средства обработки.

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

В этом сценарии использования средство обработки создает новый столбец с именем full_description путем объединения существующих столбцов — user в верхнем регистре и des. Также оно заново создает идентификатор GUID и возвращает его исходное и новое значения.

Как видно из примера выше, вы можете вызывать метод C# во время вызова метода `output.Set`.

Ниже приведен пример основного скрипта U-SQL, использующего пользовательское средство обработки.

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Использование пользовательских средств применения
Пользовательское средство применения U-SQL позволяет вызывать пользовательскую функцию C# для каждой строки, возвращаемой выражением запроса к внешней таблице. Входные данные справа оцениваются для каждой строки из входных данных слева, а затем созданные строки объединяются для итоговых выходных данных. Список столбцов, созданных оператором APPLY, — это сочетание набора столбцов из входных данных слева и справа.

Пользовательские средства применения вызываются как часть выражения USQL SELECT.

Типичный вызов пользовательских средств применения выглядит следующим образом:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Дополнительные сведения об использовании средств применения в выражении SELECT см. в статье [U-SQL SELECT, выбрав из CROSS APPLY и OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx).

Ниже представлено определение базового класса пользовательского средства применения.

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Чтобы определить пользовательское средство применения, нам необходимо создать интерфейс `IApplier` с атрибутом [`SqlUserDefinedApplier`], который не является обязательным для определения пользовательских средств применения.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Операция применения вызывается для каждой строки внешней таблицы. Она возвращает выходной набор строк `IUpdatableRow`.
* Класс Constructor используется для передачи параметров в пользовательское средство применения.

**SqlUserDefinedApplier** указывает, что тип должен быть зарегистрирован как пользовательское средство применения. Этот класс не наследуется.

Атрибут **SqlUserDefinedApplier** является **необязательным** для определения пользовательского средства применения.


Ниже представлены основные объекты, поддерживающие программирование.

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Входной набор строк передается в виде ввода `IRow`. Выходные строки создаются как интерфейс вывода `IUpdatableRow`.

Имена отдельных столбцов можно узнать, вызвав метод схемы `IRow`.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Чтобы получить фактические значения данных из входящего метода `IRow`, мы используем метод Get() интерфейса `IRow`.

```
mycolumn = row.Get<int>("mycolumn")
```

Или мы можем использовать имя столбца схемы:

```
row.Get<int>(row.Schema[0].Name)
```

Выходные значения следует передавать в выход `IUpdatableRow`:

```
output.Set<int>("mycolumn", mycolumn)
```

Важно понимать, что пользовательские средства применения только выводят столбцы и значения, определенные вызовом метода `output.Set`.

Фактический вывод данных из средства применения инициируется вызовом метода `yield return output.AsReadOnly();`.

Параметры пользовательских средств применения можно передать в конструктор. Средство применения может возвращать переменное число столбцов, которое должно быть определено при вызове средства применения из основного скрипта U-SQL.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Ниже приведен пример использования пользовательского средства применения.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

И основной скрипт U-SQL для этого пользовательского средства применения:

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

В этом сценарии пользовательское средство применения действует как анализатор значений с разделителями-запятыми для набора свойств автомобильного парка. Строки входного файла будут выглядеть так, как показано ниже.

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Это стандартный TSV-файл со значениями с разделением знаками табуляции, в котором столбец свойств содержит такие свойства автомобилей, как производитель, модель и т. д. Эти свойства нужно проанализировать и преобразовать в столбцы таблицы. Предоставленное средство применения также позволяет создать динамическое число свойств в результирующем наборе строк на основе переданных параметров. Вы можете создать либо все свойства, либо определенный набор свойств.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Пользовательское средство применения может вызываться как новый экземпляр средства применения.

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Или с вызовом метода фабрики оболочки.

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Использование пользовательских средств объединения
Пользовательское средство объединения позволяет объединить строки из левого и правого наборов строк, используя настраиваемую логику объединения. Пользовательское средство объединения используется с выражением COMBINE.

Средство объединения вызывается из выражения COMBINE, которое предоставляет необходимые сведения о входных наборах строк, столбцах для группирования, схеме ожидаемого результата, а также дополнительные сведения.

Чтобы вызвать средство объединения из основного скрипта U-SQL, используйте следующий синтаксис:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Дополнительные сведения см. в [описании выражения COMBINE для U-SQL](https://msdn.microsoft.com/library/azure/mt621339.aspx).

Чтобы задать пользовательское средство объединения, нам необходимо создать интерфейс `ICombiner` с атрибутом [`SqlUserDefinedCombiner`], который не является обязательным для определения пользовательских средств объединения.

Определение базового класса `ICombiner`.

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Пользовательская реализация интерфейса `ICombiner` должна содержать определение для переопределения метода `IEnumerable<IRow>` Combine.

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Атрибут **SqlUserDefinedCombiner** указывает, что этот тип должен быть зарегистрирован как пользовательское средство объединения. Этот класс не наследуется.

Метод **SqlUserDefinedCombiner** используется для определения свойства режима объединения. Он является необязательным атрибутом для определения пользовательских средств объединения.

Режимы CombinerMode

Перечисление CombinerMode может принимать следующие значения:

* Full (0). Каждая строка выходных данных может зависеть от всех входных строк слева и справа с одинаковым значением ключа.

* Left (1). Каждая строка выходных данных зависит от одной входной строки из левого входа (и потенциально от всех строк из правого входа с тем же значением ключа).

* Right (2) — каждая строка выходных данных зависит от одной входной строки из правого входа (и потенциально от всех строк из левого входа с тем же значением ключа).

* Inner (3). Каждая строка выходных данных зависит от одной строки из левого входа и одной строки из правого входа с тем же значением ключа.

Пример: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Ниже представлены основные объекты, поддерживающие программирование.

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Входные наборы строк передаются в виде левого (**left**) и правого (**right**) интерфейсов типа `IRowset`. Они должны быть перечислены для обработки. Каждый интерфейс можно перечислить только один раз, поэтому для повторной обработки их нужно перечислять с использованием кэша.

Для целей кэширования мы можем создать тип структуры памяти List\<T\> как результат выполнения запроса LINQ, в частности создать тип List<`IRow`>. Также для перечисления можно использовать анонимные типы данных.

Дополнительные сведения о запросах LINQ см. в статье [Introduction to LINQ Queries (C#)](https://msdn.microsoft.com/library/bb397906.aspx) (Введение в запросы LINQ (C#)), а описание интерфейса IEnumerable\<T\> — в статье [Интерфейс IEnumerable\<T\>](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx).

Чтобы получить фактические значения данных из входящего метода `IRowset`, мы используем метод Get() интерфейса `IRow`.

```
mycolumn = row.Get<int>("mycolumn")
```

Имена отдельных столбцов можно узнать, вызвав метод схемы `IRow`.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Или мы можем использовать имя столбца схемы:

```
c# row.Get<int>(row.Schema[0].Name)
```

Общее перечисление с помощью LINQ выглядит следующим образом:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Перечислив оба набора строк, мы будем в цикле перебирать все строки, выбирая для каждой строки из левого набора все строки, которые соответствуют условию, заданному для средства объединения.

Выходные значения следует передавать в выход `IUpdatableRow`.

```
output.Set<int>("mycolumn", mycolumn)
```

Фактический вывод данных инициируется вызовом метода `yield return output.AsReadOnly();`.

Ниже приведен пример средства объединения.

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

В этом сценарии использования мы создаем аналитический отчет для организации розничной торговли. Наша задача — найти все товары стоимостью более 20 000 долларов США, которые за определенный промежуток времени продавались через веб-сайт быстрее, чем через обычные точки розничной торговли.

Вот основной скрипт U-SQL: Вы можете сравнить логику обычной инструкции JOIN и средства объединения.

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Пользовательское средство объединения может вызываться как новый экземпляр объекта применения.

```
USING new MyNameSpace.MyCombiner();
```


Или с вызовом метода фабрики оболочки.

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Использование пользовательских средств редукции

U-SQL позволяет создать пользовательские средства редукции для набора строк на языке C#, используя платформу расширения для определяемых пользователем операторов и реализацию интерфейса IReducer.

Определяемые пользователем редукторы можно использовать для исключения ненужных строк в процессе извлечения (импорта) данных. Также с помощью этого средства можно обрабатывать и оценивать строки и столбцы. Оно позволяет выбрать извлекаемые строки с использованием программируемой логики.

Чтобы определить класс пользовательского средства редукции, нужно создать интерфейс `IReducer` с необязательным атрибутом `SqlUserDefinedReducer`.

Этот интерфейс класса должен содержать определение, переопределяющее интерфейс объекта `IEnumerable` для набора строк.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Атрибут **SqlUserDefinedReducer** указывает, что этот тип должен быть зарегистрирован как пользовательское средство редукции. Этот класс не наследуется.
**SqlUserDefinedReducer** — это необязательный атрибут для определения пользовательских средств редукции. Он используется для определения свойства IsRecursive.

* bool IsRecursive.    
* Значение **true** указывает, является ли это средство редукции ассоциативным и коммуникативным.

Основные объекты, поддерживающие программирование, — это **input** (ввод) и **output** (вывод). Объект input используется для перечисления входных строк, а output — для вывода строк, полученных в результате действий по редукции.

Для перечисления входных строк мы используем метод `Row.Get`.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Параметр для метода `Row.Get` — это столбец, который передается как часть класса `PRODUCE` инструкции `REDUCE` в основном скрипте U-SQL. Здесь необходимо использовать правильный тип данных.

Для выходных данных используйте метод `output.Set`.

Важно понимать, что пользовательские средства редукции возвращают только те выходные значения, которые определены в вызове метода `output.Set`.

```
output.Set<string>("mycolumn", guid);
```

Фактический вывод данных из средства редукции инициируется вызовом метода `yield return output.AsReadOnly();`.

Ниже приведен пример средства редукции.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

В этом сценарии использования средство редукции пропускает строки с пустым именем пользователя. Для каждой строки в наборе строк считывается каждый обязательный столбец, затем оценивается длина имени пользователя. Текущую строку средство передает на выход, только если длина имени пользователя превышает 0.

Ниже представлен основной скрипт U-SQL, который использует пользовательское средство редукции.

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
