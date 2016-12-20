---
title: "Руководство по программированию U-SQL для аналитики Azure Data Lake | Документация Microsoft"
description: "Руководство по программированию U-SQL"
services: data-lake-analytics
documentationcenter: 
author: MikeRys
manager: arindamc
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: mrys
translationtype: Human Translation
ms.sourcegitcommit: 8da474fbc9eae750bbd2e1f6908046d2e7e7be55
ms.openlocfilehash: 42e1d0cdde66f4bf4a6f3b23421e137716d05beb


---
# <a name="u-sql-programmability-guide"></a>Руководство по программированию U-SQL
## <a name="azure-data-lake"></a>Озеро данных Azure
Azure Data Lake предоставляет разработчикам, специалистам по обработке данных и аналитикам возможности, которые позволяют быстро сохранять данные независимо от объема, формы и скорости, а также выполнять их обработку и анализ на разных платформах и языках. Azure Data Lake упрощает получение и хранение всех ваших данных, одновременно ускоряя работу пакетной, потоковой и интерактивной аналитики.

Azure Data Lake — это набор служб, которые формируют облачную платформу для больших данных:

- HDInsight
- Хранилище озера данных Azure
- Аналитика озера данных Azure

U-SQL — это специальный язык запросов для рабочих нагрузок, обрабатывающих большие данные. U-SQL отличается использованием SQL-подобного декларативного синтаксиса, а также расширяемостью и удобством программирования, характерными для C#. Также этот язык позволяет обращаться к схемам метаданных и управлять ими или создавать пользовательские компоненты, например обработчики данных и редукторы.

В этом руководстве мы рассмотрим такие возможности программирования U-SQL на основе C#, обеспечивающие расширяемость и удобство.

## <a name="requirements"></a>Требования
Чтобы начать разработку в ADL, необходимо загрузить и установить [средства Azure Data Lake для Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="starting-with-u-sql"></a>Начало работы с U-SQL  
Описание языка U-SQL не входит в задачи этого документа. Но мы рассмотрим основные конструкции U-SQL, чтобы ознакомить вас с возможностями программирования U-SQL. Дополнительные сведения см. в [справочнике по языку U-SQL](http://aka.ms/usql_reference).

Рассмотрим следующий пример.

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

У нас есть **входной файл** input_file.tsv, который определен в **локальной переменной** @input_file..

Представленный выше скрипт U-SQL выполняет следующие действия.

* Начальные инструкции **EXTRACT** загружают данные в память, преобразуя входной файл в **набор строк в памяти**.
* Инструкция **SELECT** выполняет в наборе строк статистическую обработку строк и подготовку к экспорту.
* Команда **OUTPUT** экспортирует данные из набора строк во внешний **выходной файл**.

Сначала мы узнаем, как использовать выражения C# непосредственно в скрипте U-SQL.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Типы и выражения C# в скрипте U-SQL
Выражения C# для U-SQL, как обычные выражения C#, — это последовательность, представленная одним или несколькими операторами, которые возвращают одно значение, объект, метод или пространство имен. Выражения могут состоять из литеральных значений, вызовов методов, операторов или простых имен. Простое имя может быть именем переменной, членом типа, параметром метода, пространством имен или типом.

Когда мы говорим о выражениях C# для U-SQL, мы подразумеваем исключительно выражения C# для основного скрипта U-SQL. Код программной части C#, который обсуждается далее в этом документе, также может содержать выражения C# в качестве обычных программных элементов C#.

Выражения могут использовать операторы, которым передаются другие выражения в качестве параметров, или вызовы методов, параметры которых являются вызовами других методов. Пример выражения:  

```c#
    Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd"))
```

Язык U-SQL позволяет использовать стандартные выражения C# из встроенных пространств имен.  
 
```c#
    Microsoft.Analytics.Interfaces;  
    Microsoft.Analytics.Types.Sql;  
    System;  
    System.Collections.Generic;  
    System.Linq;  
    System.Text;  
```

В инструкциях SELECT и EXTRACT языка U-SQL можно использовать общие выражения C#.

Выражения C# также можно использовать в инструкциях DECLARE и IF. Пример такого выражения:   

```c#
    DateTime.Today.Day   
    Convert.ToDateTime
```

Пример основного скрипта U-SQL:  

```sql
    DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");
```

Выражения C# включают расширенные возможности для обработки столбцов в составе набора строк. Например, если требуется преобразовать столбец datetime в дату, содержащую нулевое значение времени, можно использовать такую строку SELECT в основном скрипте U-SQL:

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
     MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd")))
AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Как вы видите, для преобразования мы используем метод `System.Convert.ToDateTime`.

Следующий чуть более сложный сценарий предполагает использование некоторых базовых операторов C#. Взгляните на следующее выражение.

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
          MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Это пример выражения C# с условным оператором.

Приведенный выше пример показывает, как использовать выражения C# в основном скрипте U-SQL. Но U-SQL допускает и более широкое использование программных средств, как описано дальше в этом документе.  

Полный скрипт:

```sql
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
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-data-type-conversions"></a>Использование выражений C# для преобразования типов данных
В примере выше показано, как можно преобразовать данные типа datetime с помощью выражений C#. В этом конкретном случае строковые данные о дате и времени преобразуются в стандартное значение datetime использованием формата времени полуночи (00:00:00).

```sql
DECLARE @dt String = "2016-07-06 10:23:15";
@rs1 =
    SELECT Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
           dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-todays-date"></a>Использование выражений C# для получения текущей даты
Получить текущую дату можно с помощью следующего выражения C#:

```c#
DateTime.Now.ToString("M/d/yyyy")
```

В скрипте:

```sql
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

## <a name="in-line-c-function-expressions"></a>Выражения со встроенными функциями C#
U-SQL позволяет использовать выражения со встроенными функциями в составе выражений C#. Это позволяет использовать функции C# с выходными ссылочными параметрами.

Шаблон определения для выражения со встроенной функцией

```c#
    (Func<type of param1, type of param2>)
    (param1 =>
         { … return param2}
    ) (Rowset Column)
```

Пример:

```c#
    (Func<string, DateTime?>)
    (input_p =>
         {
            DateTime dt_result;
            return DateTime.TryParse(input_p, out dt_result)
                   ? (DateTime?) dt_result : (DateTime?) null;
         }
    )
    ) (dt)
```

В этом примере мы определим встроенную функцию со строковым входным параметром input_p. В этой функции мы проверим, представляет ли полученная строка допустимое значение даты и времени. Если да, функция вернет это значение, в противном случае она вернет значение null.

В этом случае мы используем встроенную функцию, так как функция DateTime.TryParse содержит выходной параметр `out dt_result`. Мы определим его как `DateTime dt_result`.


## <a name="verifying-data-type-values"></a>Проверка значений типов данных
Некоторые функции C# нельзя использовать напрямую в основном скрипте U-SQL как выражение C#. В частности, это касается функций, которые требуют ссылочный выходной параметр. Но эти функции можно определить и использовать как встроенные функции, как мы уже показали выше.

### <a name="using-inline-function-expressions"></a>Использование выражений со встроенной функцией
Чтобы проверить допустимость значения DateTime, можно использовать `DateTime.TryParse`.

Ниже приведен полный пример выражения со встроенной функцией, используемого для проверки значения типа данных с помощью `DateTime.TryParse`.

В этом примере мы проверим значение типа DateTime.

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt string,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN((
             (Func<string, DateTime?>)
             (input_parameter =>
                { DateTime dt_result;
                  return
                     DateTime.TryParse(input_parameter, out dt_result)
                       ?(DateTime?) dt_result : (DateTime?) null;
                }
             )
             ) (dt)) AS start_time,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-code-behind"></a>Использование Code Behind
Чтобы применить описанные возможности в примере U-SQL с использованием компонента Code Behind, мы определим функцию C# ToDateTime.

Для этого нам нужно изменить следующий сегмент основного скрипта U-SQL выше:

```sql
     @rs1 =
        SELECT
          MAX(guid) AS start_id,
          MIN(USQL_Programmability.CustomFunctions.ToDateTime(dt)) AS start_time,
          DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
          user,
          des
          FROM @rs0
        GROUP BY user, des;
```

А вот функция Code Behind:

```c#
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }
```

## <a name="using-code-behind"></a>Использование Code Behind
Code Behind — это компонент, который обеспечивает возможность использования кода C# в проекте U-SQL. По сути, Code Behind — это компилируемая сборка (DLL), на которую ссылается скрипт U-SQL. Средства Visual Studio позволяют управлять кодом C#, включая отладку, в составе проекта U-SQL.

Когда в Visual Studio создается обычный проект U-SQL, он состоит из двух частей. Основной скрипт — файл с расширением **.usql**.

![a](./media/data-lake-analytics-u-sql-programmability-guide/base-script-file.png)


Типичный проект решения   
![typical-solution-project](./media/data-lake-analytics-u-sql-programmability-guide/typical-solution-project.png)


Вторая часть проекта представлена компонентом Code Behind — это файл Script.usql.cs  
![code-behind](./media/data-lake-analytics-u-sql-programmability-guide/code-behind.png)

Этот файл содержит стандартные определения пространства имен для объектов, поддерживающих программирование.

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{

}
```

Выше представлен шаблон Code Behind, который создается автоматически. Этот файл содержит стандартные определения пространства имен для объектов, поддерживающих программирование. При выполнении проекта он компилируется и ссылочно используется в основном скрипте U-SQL.

Чтобы начать разработку объектов, поддерживающих программирование, нужно создать открытый (**public**) класс.

```c#
namespace USQL_Programmability
{
    public class MyClass
    {
        public static string MyFunction(string param1)
        {
            return "my result";
        }

    }

}
```

Объектами, поддерживающими программирование, могут быть определяемые пользователем функции (**UDF**), определяемые пользователем типы (например, **UDT, PROCESS, REDUCER**) и т. п.

## <a name="registering-u-sql-assemblies"></a>Регистрация сборок U-SQL
Модель расширяемости U-SQL реализована как возможность добавлять собственный код. Сейчас U-SQL позволяет возможность легко добавлять собственный код на основе .NET, в частности на языке C#. Но вы можете использовать пользовательский код, написанный на других языках .NET, таких как F# или VB.Net. Вы даже можете развернуть собственную среду выполнения для других языков, хотя для этого вам придется самостоятельно обеспечить взаимодействие с ней через уровень .NET. Если вы хотите, чтобы мы добавили поддержку для определенного языка, оставьте запрос на функцию или комментарий на странице http://aka.ms/adlfeedback

### <a name="difference-between-code-behind-and-assembly-registration-through-adl-tools-in-visual-studio"></a>Отличия при регистрации компонента Code Behind и других сборок с помощью средств ADL в Visual Studio
Проще всего использовать собственный код с помощью средств ADL для работы с компонентом Code Behind в Visual Studio.

Как уже говорилось, пользовательский код для скрипта (например, Script.usql) вы включаете в файл Code Behind (например, Script.usql.cs).

![code-behind-example](./media/data-lake-analytics-u-sql-programmability-guide/code-behind-example.jpg)
**Рис. 1**. Пример использования Code Behind в средствах ADL для VS (щелкните изображение, чтобы увеличить его; исходный код примера см. [здесь](https://github.com/Azure/usql/tree/master/Examples/TweetAnalysis))
<br />

Компонент Code Behind удобен тем, что автоматически выполняет за вас следующие действия при передаче скрипта.  

1. Создает сборку для файла Code Behind.  

1. Добавляет в скрипт пролог, который с помощью инструкции [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) регистрирует файл сборки, и с помощью инструкции [REFERENCE ASSEMBLY] (https://msdn.microsoft.com/library/azure/mt763294.aspx) загружает эту сборку в контекст скрипта.

1. Добавляет в скрипт эпилог, который с помощью инструкции [DROP ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763295.aspx) удаляет временно зарегистрированную сборку.

Чтобы просмотреть созданные пролог и эпилог, откройте скрипт:

![generated-prologue](./media/data-lake-analytics-u-sql-programmability-guide/generated-prologue.png)
**Рис. 2**. Автоматически созданные пролог и эпилог для файла Code Behind
<br />

Ниже перечислены некоторые недостатки использования Code Behind.

* Код загружается заново при каждой передаче скрипта.
* Функции недоступны другим пользователям.

Но вы можете добавить в решение отдельную библиотеку классов C# для U-SQL (см. рис. 3), создать новый код или скопировать существующий файл Code Behind (изменять код C# не нужно; см. рис. 4), а затем зарегистрировать сборку с помощью соответствующего пункта меню (см. шаг 1 на рис. 5).

![creating-project](./media/data-lake-analytics-u-sql-programmability-guide/creating-project.png)
**Рис. 3**. Создание проекта кода C# для U-SQL  
<br />

![class-library](./media/data-lake-analytics-u-sql-programmability-guide/class-library.png)
**Рис. 4**. Библиотека классов C# для U-SQL и файл Code Behind  
<br />

![register-code](./media/data-lake-analytics-u-sql-programmability-guide/register-code.png)
**Рис. 5**Регистрация проекта кода C# для U-SQL
<br />

В диалоговом окне регистрации (см. шаг 2 на рис. 5) можно указать расположение для регистрации сборки (учетную запись Data Lake Analytics и базу данных) и имя сборки (локальный путь к сборке заполняется автоматически). Также вы можете повторно зарегистрировать уже зарегистрированную сборку и добавить дополнительные зависимости с помощью двух параметров.

* *Управляемые зависимости*: здесь указываются дополнительные необходимые управляемые сборки. Каждая выбранная сборка регистрируется отдельно; на нее можно ссылаться в скриптах. Этот вариант применим и к другим сборкам .NET.

* *Дополнительные файлы*: позволяет добавлять дополнительные файлы ресурсов, необходимые для сборки. Они регистрируются вместе с сборкой и автоматически загружаются при обращении к этой сборке. Это хороший вариант для файлов конфигурации, собственных сборок, других сред выполнения, их ресурсов и т. д.

В приведенных ниже примерах мы используем оба варианта. В этой [записи блога](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/18/introducing-image-processing-in-u-sql/) об обработке изображений представлен еще один пример применения предопределенной сборки с использованием описанных параметров регистрации.

Теперь вы сможете ссылаться на зарегистрированные сборки из любого скрипта U-SQL, у которого есть разрешения на обращение к базе данных, в которой зарегистрированы эти сборки (см. код в скрипте U-SQL на рис. 4). Нужно добавить явную ссылку на каждую отдельно зарегистрированную сборку. Дополнительные файлы ресурсов будут развернуты автоматически. Этот скрипт не должен содержать файл Code Behind, входящий в связанные сборки. Но вам ничто не мешает добавить другой код.

### <a name="registering-assemblies-via-adl-tools-in-visual-studio-and-in-u-sql-scripts"></a>Регистрация сборок с помощью средств ADL в Visual Studio и скриптов U-SQL
Хотя средства ADL в Visual Studio позволяют легко зарегистрировать сборку, эту же операцию вы можете выполнить самостоятельно с помощью скрипта (точно так же, как это делают средства). Это удобно, например, если вы уже создали нужные сборки на другой платформе разработки, и теперь хотите их отправить и зарегистрировать. Для этого выполните следующие действия.

1. Передайте сборку в виде библиотеки DLL, все дополнительные несистемные библиотеки DLL и все файлы ресурсов в расположение в пределах учетной записи хранения Azure Data Lake или учетной записи хранилища BLOB-объектов Microsoft Azure, связанной с вашей учетной записью Azure Data Lake. Можно использовать любое из множества доступных средств передачи: команды Powershell, функцию передачи в Data Lake Explorer из набора средств ADL для Visual Studio, ваше любимое средство передачи для пакетов SDK или портал Azure.

1. Загрузив библиотеки DLL, используйте инструкцию [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx), чтобы зарегистрировать их.

Мы применим этот подход в следующем примере с пространственной сборкой.

### <a name="registering-assemblies-that-use-other-net-assemblies-based-on-the-json-and-xml-sample-library"></a>Регистрация сборок, которые используют другие сборки .NET (на основе образца библиотеки JSON и XML)
На нашем [сайте Github](https://github.com/Azure/usql/) доступны образцы сборок U-SQL для общего использования. Одна из этих сборок с именем [Microsoft.Analytics.Samples.Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats) предоставляет средства извлечения, функции и средства вывода для обработки документов JSON и XML. Сборка Microsoft.Analytics.Samples.Formats зависит от двух существующих сборок для доменов, которые выполняют обработку данных JSON и XML. Для обработки документов JSON используется библиотека [Newtonsoft Json.Net](http://www.newtonsoft.com/), а для обработки XML — библиотека [System.Xml](https://msdn.microsoft.com/data/bb291078.aspx). Мы покажем, как их можно зарегистрировать и использовать в скриптах.

Сначала мы скачаем [проект Visual Studio](https://github.com/Azure/usql/tree/master/Examples/DataFormats) в локальную среду разработки (например, создав локальную копию с помощью средства GitHub для Windows). Затем мы откроем решение в Visual Studio, где нужно щелкнуть проект правой кнопкой мыши для регистрации сборки, как описано выше. Хотя эта сборка имеет два зависимости, нам нужно включить только Newtonsoft, так как System.Xml уже доступна в Azure Data Lake (но нам нужно указать на нее явную ссылку). На рис. 6 показано, как мы присваиваем сборке имя (обратите внимание, что можно выбрать и другое имя без точек) и добавляем библиотеку Newtonsoft. Каждая из двух сборок теперь будет теперь отдельно зарегистрирована в указанной базе данных (например, JSONBlog).

![register-assembly](./media/data-lake-analytics-u-sql-programmability-guide/register-assembly.png)
**Рис. 6**. Регистрация сборки Microsoft.Analytics.Samples.Formats с помощью Visual Studio
<br />

Если вам или другим пользователям зарегистрированных сборок, которым вы предоставили доступ на чтение базы данных, нужно использовать функцию JSON в собственных скриптах, следует добавить в скрипт следующие две ссылки:

```
REFERENCE ASSEMBLY JSONBlog.[NewtonSoft.Json];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

А если вы хотите использовать возможности работы с XML, добавьте ссылки на системную и зарегистрированную сборку:

```
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

Дополнительные сведения о том, как работать с JSON, см. в [этой записи блога](https://blogs.msdn.microsoft.com/mrys/?p=755).

### <a name="registering-assemblies-that-use-native-c-assemblies-using-the-sql-server-2016-spatial-type-assembly-from-the-feature-pack"></a>Регистрация сборок, которые используют собственные сборки C++ (с помощью сборки пространственного типа для SQL Server 2016 из пакета дополнительных компонентов)
Теперь давайте рассмотрим немного другой сценарий. Предположим, что нужная вам сборка зависит не от кода, который основан на .NET, а например от машинной сборки C++. Пример такой сборки — сборка типа SQL Server [Microsoft.SqlServer.Types.dll](https://www.microsoft.com/download/details.aspx?id=52676). Она предоставляет реализацию типов hierarchyID, geometry и geography для SQL Server на основе .NET и предназначена для использования в клиентских приложениях SQL Server для обработки типов SQL Server (до выпуска SQL Server 2016 эта сборка предоставляла реализацию пространственных типов SQL Server).

Давайте узнаем, как зарегистрировать эту сборку в U-SQL.

Сначала нужно скачать и установить саму сборку из [пакета дополнительных компонентов SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=52676). Выберите 64-разрядную версию установщика (ENU\x64\SQLSysClrTypes.msi), так как нам нужны именно 64-разрядные версии библиотек.

Установщик устанавливает управляемую сборку Microsoft.SqlServer.Types.dll в каталог C:\Program Files (x86)\Microsoft SQL Server\130\SDK\Assemblies и машинную сборку SqlServerSpatial130.dll в каталог \Windows\System32\.. Теперь мы можем загрузить эти сборки в Azure Data Lake Store (например, в папку с именем /upload/asm/spatial).

Так как установщик устанавливает собственную библиотеку в системную папку c:\Windows\System32, мы должны либо скопировать файл SqlServerSpatial130.dll в другую папку перед отправкой, либо использовать средство, которое не [перенаправляет файловую систему](https://msdn.microsoft.com/library/windows/desktop/aa384187(v=vs.85).aspx) системных папок. Например, если вы хотите передать файл с помощью текущей версии обозревателя файлов ADL Visual Studio, необходимо сначала скопировать файл в другой каталог. В противном случае (по состоянию на момент написания этого документа) вы передадите 32-разрядную версию файла (так как Visual Studio — это 32-разрядное приложение, которое перенаправляет файловую систему в окне ADL для выбора передаваемого файла). В таком случае при выполнении скрипта U-SQL, который обращается к машинной сборке, вы увидите следующую (внутреннюю) ошибку выполнения:

*Внутреннее исключение в пользовательском выражении. Сделана попытка загрузить программу с неправильным форматом. (Исключение из HRESULT: 0x8007000B.)*

Отправив два файла сборок, нам нужно зарегистрировать их в базе данных SQLSpatial с помощью следующего скрипта:

```sql
DECLARE @ASSEMBLY_PATH string = "/upload/asm/spatial/";
DECLARE @SPATIAL_ASM string = @ASSEMBLY_PATH+"Microsoft.SqlServer.Types.dll";
DECLARE @SPATIAL_NATIVEDLL string = @ASSEMBLY_PATH+"SqlServerSpatial130.dll";

CREATE DATABASE IF NOT EXISTS SQLSpatial;
USE DATABASE SQLSpatial;

DROP ASSEMBLY IF EXISTS SqlSpatial;
CREATE ASSEMBLY SqlSpatial
FROM @SPATIAL_ASM
WITH ADDITIONAL_FILES =
     (
         @SPATIAL_NATIVEDLL
     );
```

В нашем примере мы зарегистрировали только одну сборку U-SQL и включили машинную сборку в эту сборку U-SQL как строку зависимости. Чтобы использовать пространственные сборки, достаточно указать ссылку на сборку U-SQL, и дополнительный файл станет доступным для сборки автоматически. Ниже приведен простой пример скрипта с использованием пространственной сборки:

```sql
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY SQLSpatial.SqlSpatial;

USING Geometry = Microsoft.SqlServer.Types.SqlGeometry;
USING Geography = Microsoft.SqlServer.Types.SqlGeography;
USING SqlChars = System.Data.SqlTypes.SqlChars;

@spatial =
    SELECT * FROM (VALUES
                   // The following expression is not using the native DDL
                   ( Geometry.Point(1.0,1.0,0).ToString()),    
                   // The following expression is using the native DDL
                   ( Geometry.STGeomFromText(new SqlChars("LINESTRING (100 100, 20 180, 180 180)"), 0).ToString())
                  ) AS T(geom);

OUTPUT @spatial
TO "/output/spatial.csv"
USING Outputters.Csv();
```

Библиотека типов SQL зависима от сборки System.XML, поэтому нужно указать ссылку на нее. Кроме того, некоторые методы используют типы System.Data.SqlTypes вместо встроенных типов C#. Файл System.Data уже включен по умолчанию, и теперь достаточно просто указать необходимый тип SQL. Приведенный выше код можно найти на нашем [сайте GitHub](https://github.com/Azure/usql/tree/master/Examples/SQLSpatialExample).


### <a name="assembly-versioning-and-other-points"></a>Управление версиями сборок и другие замечания
Сейчас в U-SQL используется .NET Framework 4.5. Обязательно убедитесь, что ваши сборки совместимы с этой версией среды выполнения!

Как упоминалось выше, U-SQL выполняет код в 64-разрядном формате (x64). Поэтому код нужно компилировать так, чтобы он выполнялся в среде x64. В противном случае вы получите ошибку неправильного формата, как показано выше.

Любой передаваемый в хранилище файл (библиотеки DLL, файлы ресурсов, включая другие среды выполнения, машинные сборки, файлы конфигурации и т.д.) не может быть более 400 МБ. Общий размер всех ресурсов, развернутых с помощью инструкции DEPLOY RESOURCE или с использованием ссылок на сборки и связанные файлы, не может превышать 3 ГБ.

И наконец, важно помнить, что в каждой базе данных U-SQL может быть только одна версия любой сборки. Например, если вам одновременно нужны версии 7 и 8 библиотеки NewtonSoft Json.Net, их следует зарегистрировать их в разных базах данных. Кроме того, каждый скрипт может ссылаться только на одну версию библиотеки DLL для каждой сборки. В этом отношении U-SQL соблюдает семантику C# управления сборками и версиями.


## <a name="user-defined-functions---udf"></a>Определяемые пользователем функции
В языке U-SQL концепция определяемых пользователем функций обозначает подпрограммы, которые принимают параметры, выполняют действия, например сложные вычисления, и возвращают результат этого действия в виде определенного значения. Определяемая пользователем функция может возвращать только одно скалярное значение. Основной скрипт U-SQL может вызывать такие функции, как и любую другую скалярную функцию C#.

Определяемые пользователем функции U-SQL должны инициализироваться как общедоступные (public) и статические (**static**).

```c#
        public static string MyFunction(string param1)
        {
            return "my result";
        }
```

Сначала давайте рассмотрим простой пример создания определяемой пользователем функции.

В этом сценарии нужно определить финансовый период (квартал и месяц) для первого входа в систему определенного пользователя. Финансовый год для этого примера начинается в июне.

Чтобы вычислить финансовый период, мы создаем следующую функцию C#.

```c#
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

Она вычисляет финансовый месяц и квартал и возвращает строковое значение. Для июня (первый месяц первого финансового квартала) возвращается значение Q1:P1, для июля — Q1:P2 и т. д.

В нашем проекте U-SQL мы будем использовать базовые функции C#.

Вот как выглядит раздел кода программной части для этого примера:

```c#
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

Теперь мы вызовем эту функцию из основного скрипта U-SQL. Для этого нам нужно предоставить полное имя функции, включая пространство имен, в формате: ПространствоИмен.Класс.Функция(параметр).

```sql
    USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Так будет выглядеть вызов в основном скрипте U-SQL:

```sql
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

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

А это — файл выходных данных с результатом выполнения скрипта:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Это простой пример использования встроенной определяемой пользователем функции в U-SQL.

### <a name="keeping-state-between-udf-invocations"></a>Сохранение состояния между вызовами определяемой пользователем функции
Объекты, поддерживающие программирование C# в U-SQL, могут быть более сложными. Например, они могут интерактивно взаимодействовать через глобальные переменные Code Behind. Рассмотрим следующий сценарий использования:

В больших организациях пользователи могут переключаться между разными внутренними приложениями, включая Microsoft Dynamics CRM, Power BI, и т. д. Клиент хочет применить анализ телеметрии для пользовательских переключений между приложениями, например оценить тенденции использования и т. д. Конечная цель для бизнеса — оптимизировать использование приложений. Возможно, при этом удастся объединить некоторые приложения или процедуры входа.

Для достижения этой цели мы должны определить идентификаторы сеансов и интервалы (функция LAG) между последними сеансами.

Нам нужно найти предыдущий вход и привязать этот вход ко всем сеансам, создаваемым для одного и того же приложения. Первая трудность заключается в том, что основной скрипт U-SQL не позволяет производить вычисления с функцией LAG над уже вычисленным столбцом. Вторая трудность — мы хотим сохранить определенный сеанс для всех сеансов за тот же интервал.

Чтобы решить эту проблему, мы применим глобальную переменную в разделе Code Behind: `static public string globalSession;`.

Эта глобальная переменная применяется для всего набора строк во время выполнения скрипта.

Так будет выглядеть раздел Code Behind для нашей программы U-SQL:

```c#
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
                if (timeGap <= 60)
                    return Session;
                else
                    return Guid.NewGuid().ToString();
            }
            else
                return Guid.NewGuid().ToString();

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session))
            {
                globalSession = Session;
            }
            return globalSession;
        }

    }
}
```

Глобальная переменная `static public string globalSession;` используется внутри функции `getStampUserSession` и инициализируется заново каждый раз, когда изменяется значение параметра Session.

Основной скрипт U-SQL:

```sql
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
    SELECT EventDateTime,
           UserName,
LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
           USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           )
           AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT EventDateTime,
           UserName,
           LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
           USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
TO @out2
ORDER BY UserName, EventDateTime ASC
USING Outputters.Csv();
```

Функция `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` вызывается здесь во время вычисления второго набора строк в памяти. Она передает столбец `UserSessionTimestamp` и возвращает значение интервала с момента изменения `UserSessionTimestamp`.

Выходной файл:

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

В этом примере вы видим более сложный сценарий использования, в котором глобальная переменная применяется в разделе Code Behind для всего набора строк в памяти.

## <a name="using-user-defined-types---udt"></a>Использование определяемых пользователем типов данных
Определяемые пользователем типы — это еще одно средство, поддерживающее возможность программирования в U-SQL. Определяемый пользователем тип в U-SQL действует так же, как аналогичный тип в C#. C# — это строго типизированный язык, в котором можно использовать встроенные и пользовательские типы данных.

Сейчас U-SQL не поддерживает неявную сериализацию и десериализацию определяемых пользователем типов данных с использованием внешних файлов. Поэтому для сериализации и десериализации методов, входящих в определение таких типов, нужно определить интерфейс IFormatter. В ADLA версии 1 поддерживается только промежуточная сериализация. Следовательно, хотя IFormatter требуется для внутренней обработки определяемых пользователем типов, его нельзя использовать для постоянной сериализации в средствах EXTRACTOR и OUTPUTTER. При записи данных в файл с помощью OUTPUTTER или чтении с помощью средства EXTRACTOR нужно сериализовать данные с определяемым пользователем типом в строковое значение с помощью метода ToString() из реализации этого типа. Или же можно при работе с этим типом применять пользовательские средства EXTRACTOR и OUTPUTTER.  

При попытке применения определяемого пользователем типа в средствах EXTRACTOR и OUTPUTTER (на основе инструкции SELECT)

```sql
@rs1 =
    SELECT MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

мы получим такую ошибку:

```
    Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
    MyNameSpace.Myfunction_Returning_UDT.

    Description:

    Outputters.Text only supports built-in types.

    Resolution:

    Implement a custom outputter that knows how to serialize this type or call a serialization method on the type in
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

* Добавьте следующие пространства имен:

```c#
    using Microsoft.Analytics.Interfaces
    using System.IO;
```

* Для определяемого пользователем типа требуется `Microsoft.Analytics.Interfaces`, а для определения интерфейса IFormatter может потребоваться `System.IO`.

* Определите пользовательский тип с атрибутом SqlUserDefinedType.

**SqlUserDefinedType** указывает, что определение типа в сборке представляет определяемый пользователем тип данных для U-SQL. Свойства этого атрибута отражают физические характеристики определяемого пользователем типа данных. Этот класс не наследуется.

SqlUserDefinedType является обязательным атрибутом для определяемых пользователем типов данных.

Конструктор для этого класса следующий.  

* SqlUserDefinedTypeAttribute(Type formatter).

* Type formatter — обязательный параметр, определяющий форматирование для определяемого пользователем типа данных. В частности, здесь передается тип интерфейса `IFormatter`.

```c#
    [SqlUserDefinedType(typeof(MyTypeFormatter))]
      public class MyType
           {
             …
           }
```

* Обычно определяемый пользователем тип данных требует определить интерфейс IFormatter:

```c#
       public class MyTypeFormatter : IFormatter<MyType>
        {
            public void Serialize(MyType instance, IColumnWriter writer,
                           ISerializationContext context)
            {
        …
            }

            public MyType Deserialize(IColumnReader reader,
                                  ISerializationContext context)
            {
        …
            }
        }
```

Интерфейс `IFormatter` для сериализации и десериализации графа объекта с корневым типом \<typeparamref name="T">.

\<typeparam name="T"> Это корневой тип графа объекта для сериализации и десериализации.

* Десериализация — процесс, обратный сериализации данных в предоставленном потоке, в ходе которого воспроизводится граф объектов.

* Сериализация — процесс сериализации объекта или графа объектов с использованием заданного корня в предоставленный поток.

Экземпляр `MyType` — экземпляр типа.  
Модуль записи `IColumnWriter` или чтения `IColumnReader` — базовый поток столбца.  
Контекст `ISerializationContext` — перечисление, которое определяет набор флагов, указывающих контекст источника или назначения для потока во время сериализации. 
 
    * *Intermediate* — указывает, что исходный или целевой контекст не является материализованным хранилищем.

    * *Persistent* — указывает, что исходный или целевой контекст является материализованным хранилищем.

Как и в C#, определение пользовательского типа данных в U-SQL может переопределять базовые операторы, например +, ==, != и т. д. Оно может содержать статические методы и т. д. Например, если мы собираемся использовать определяемый пользователем тип данных в качестве параметра статистической функции MIN, нужно переопределить оператор <.

Ранее в этом руководстве мы продемонстрировали пример, в котором определяли финансовый период для конкретной даты в формате Qn:Pn (Q1:P10). Следующий пример показывает, как использовать определяемый пользователем тип данных для значений финансового периода.

Раздел Code Behind с определяемым пользователем типом данных и интерфейсом IFormatter для него:

```c#
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

Определяемый тип включает два числа: Quarter (квартал) и Month (месяц). Здесь определены операторы ==, ! =, >, < и статический метод ToString().

Как упоминалось ранее, определяемый пользователем тип данных можно применять в выражении SELECT, но нельзя использовать в средствах вывода или извлечения без настраиваемой сериализации. Нужно сериализовать его в строку с помощью метода ToString() или использовать пользовательское средство OUTPUTTER или EXTRACTOR.

Теперь давайте рассмотрим, как можно применять определяемый пользователем тип данных. В разделе Code Behind мы заменили функцию GetFiscalPeriod этим кодом:

```c#
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

Как видите, он возвращает значение с типом данных FiscalPeriod, определенным нами.

А теперь мы используем этот тип в основном скрипте U-SQL. В этом примере представлено несколько способов вызова определяемого пользователем типа данных из скрипта U-SQL.

```sql
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
    SELECT guid AS start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs0;

@rs2 =
    SELECT start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

Раздел Code Behind полностью:

```c#
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

### <a name="udts-from-built-in-types"></a>Определяемые пользователем типы на основе встроенных типов
Скоро

## <a name="user-defined-aggregates-udagg"></a>Определяемые пользователем статистические функции
Определяемые пользователем статистические функции — это любые функции статистической обработки, не входящие в U-SQL. Например, это может быть статистическая функция для пользовательских математических вычислений, объединения строк или манипуляций со строками и т. д.

Определение базового класса для пользовательской статистической функции:

```c#
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

Атрибут SqlUserDefinedType является **необязательным** для определяемой пользователем статистической функции.


Базовый класс позволяет передать три абстрактных параметра — два входных и один результирующий. Типы данных представлены переменными, которые должны быть определены при наследовании классов:

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
        …
        }

        public override void Accumulate(string guid, string user)
        {
        …
        }

        public override string Terminate()
        {
        …
        }

    }
```

* Метод Init предоставляет процедуры инициализации для каждой группы статистической обработки. Он вызывается один раз для каждой группы во время вычисления.
* Метод Accumulate выполняется один раз для каждого значения. Он выполняет основную работу алгоритма статистической обработки. Его можно использовать для обработки значений с разными типами данных, определяемыми при наследовании класса. Может принимать два параметра разных типов данных.
* Метод Terminate выполняется один раз для каждой группы статистической обработки, в конце обработки, для вывода результатов по каждой группе.

Чтобы объявить правильные типы данных для входа и выхода, используйте определение класса.

```c#
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1 — первый параметр для метода Accumulate.
* T2 — второй параметр для метода Accumulate.
* TResult — возвращаемый тип для метода Terminate.

Например:

```c#
    public class GuidAggregate : IAggregate<string, int, int>
```

или

```c#
    public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="using-udagg-in-u-sql"></a>Использование определяемой пользователем статистической функции в U-SQL
Чтобы применить пользовательскую статистическую функцию, определите ее в разделе Code Behind или укажите ссылку на нее из существующих DLL для программирования, как описано выше.

Затем используйте такой синтаксис:

```c#
    AGG<UDAGG_functionname>(param1,param2)
```

Пример определяемой пользователем статистической функции:

```c#
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

И основной скрипт U-SQL:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

В этом сценарии мы объединяем значения идентификаторов класса для определенных пользователей.

## <a name="user-defined-objects-udo"></a>Определяемые пользователем объекты
U-SQL предоставляет возможность определять пользовательские объекты, поддерживающие программирование (определяемые пользователем объекты).

Ниже приведен список определяемых пользователем объектов в U-SQL

* Пользовательские средства извлечения
    * Извлечение по строкам.
    * Извлечение данных из структурированных файлов в пользовательском формате данных.

* Пользовательские средства вывода
    * Вывод по строкам.
    * Вывод пользовательских типов данных или вывод в пользовательский формат файла.

* Пользовательские средства обработки
    * Получение одной строки и возврат одной строки.
    * Сокращение числа столбцов или создание нового столбца, значения которого основаны на значениях существующего набора столбцов.

* Пользовательские средства применения
    * Получение одной строки и возврат от 0 до n строк.
    * Использование с инструкциями OUTER и CROSS APPLY.

* Пользовательские средства объединения
    * Объединение наборов строк — определяемых пользователем инструкций JOIN.

* Пользовательские средства редукции
    * Получение n строк и возврат одной строки.
    * Сокращение числа строк.

Обычно определяемые пользователем объекты вызываются явным образом в скрипте U-SQL при выполнении следующих инструкций U-SQL:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

## <a name="user-defined-extractor"></a>Пользовательские средства извлечения
U-SQL позволяет импортировать внешние данные с помощью инструкции EXTRACT. Инструкция EXTRACT может использовать встроенные средства извлечения для определяемых пользователем объектов.  

* *Extractors.Text()*: извлекает данные из текстовых файлов с разделителями в разных кодировках.

* *Extractors.Csv()*: извлекает данные из файлов данных с разделителями-запятыми (CSV) в разных кодировках.

* *Extractors.Csv()*: извлекает данные из файлов со значением с разделением знаками табуляции (TSV), в разных кодировках.

Но иногда может быть полезным разработать собственное средство извлечения. Например, если при импорте данных нам нужно сделать следующее.

* Изменить входные данные, разделяя столбцы или изменяя отдельные значения. Объединение столбцов лучше выполнять с помощью средства PROCESSOR.
* Синтаксический анализ неструктурированных данных, например веб-страниц или сообщений электронной почты, или частично структурированных данных, например документов XML или JSON.
* Анализ данных в неподдерживаемых кодировках

Чтобы определить пользовательское средство извлечения, нужно создать интерфейс `IExtractor`. Все входные параметры средства извлечения, такие как разделители столбцов и строк, кодировки и т. д., должны быть определены в конструкторе класса. Интерфейс `IExtractor` также должен содержать определение для переопределения `IEnumerable<IRow>`.

```c#
     [SqlUserDefinedExtractor]
     public class SampleExtractor : IExtractor
     {
         public SampleExtractor(string row_delimiter, char col_delimiter)
         {
            …

         }

         public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
         {
             …
         }
     }
```

Атрибут **SqlUserDefinedExtractor** указывает, что этот тип должен быть зарегистрирован как пользовательское средство извлечения. Этот класс не наследуется.

SqlUserDefinedExtractor — это необязательный атрибут для определения пользовательского средства извлечения. Он используется для определения свойства AtomicFileProcessing:

* bool AtomicFileProcessing.   

* Значение **true** указывает, что это средство извлечения требует предоставлять атомарные выходные файлы (JSON, XML, ...).
* Значение **false** указывает, что это средство извлечения умеет работать с разделенными и(или) распределенными файлами (CSV, SEQ,...).

Основные объекты в пользовательских объектах, поддерживающих программирование, — это *input* (ввод) и *output* (вывод). Oбъект Input используется для перечисления входных данных как `IUnstructuredReader`, а объект *Output* — для вывода данных, полученных в результате действий по извлечению.

Обращение к входным данным выполняется через `System.IO.Stream` и `System.IO.StreamReader`.

Для перечисления входных столбцов мы сначала разбиваем входной поток, используя разделитель строк:

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
    }
```

Затем еще раз разбиваем входную строку на столбцы:

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
        string[] parts = line.Split(my_column_delimiter);
            foreach (string part in parts)
        {
        …
        }
    }
```

Чтобы сохранить выходные данные, мы используем метод `output.Set`.

Важно понимать, что пользовательские средства извлечения будут возвращать только те выходные столбцы и значения, которые определены для выхода. Вызов метода Set:

```c#
    output.Set<string>(count, part);
```

Фактический вывод данных из средства извлечения инициируется вызовом метода `yield return output.AsReadOnly();`.

Пример использования средства извлечения:

```c#
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

В этом сценарии средство извлечения восстанавливает идентификаторы для столбца guid и преобразует значения столбца user в верхний регистр. Пользовательские средства извлечения могут создавать более сложные результаты, изучая и преобразовывая входные данные.

Основной скрипт U-SQL, который использует пользовательское средство извлечения:

```sql
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

## <a name="user-defined-outputter"></a>Пользовательские средства вывода
Пользовательское средство вывода — это еще один определяемый пользователем объект U-SQL, который позволяет расширить встроенные возможности U-SQL. Как и средства извлечения, существует несколько встроенных средств вывода.

* *Outputters.Text()*: записывает данные в текстовые файлы с разделителями в разных кодировках.
* *Outputters.Csv()*: записывает данные в файлы с разделителями-запятыми (CSV) в разных кодировках.
* *Outputters.Tsv()*: записывает данные в файлы со значениями с разделением знаками табуляции (TSV) в разных кодировках.

Пользовательские средства вывода позволяют записывать данные в определяемом пользователем формате. Это может быть полезно в следующих случаях.

* Запись данных в частично структурированные или неструктурированные файлы.
* Запись данных в неподдерживаемых кодировках.
* Преобразование выходных данных или добавление пользовательских атрибутов.

Чтобы определить пользовательское средство вывода, нужно создать интерфейс `IOutputter`.

Реализация базового класса `IOutputter`:

```c#
    public abstract class IOutputter : IUserDefinedOperator
    {
        protected IOutputter();

        public virtual void Close();
        public abstract void Output(IRow input, IUnstructuredWriter output);
    }
```

Все входные параметры средства вывода, такие как разделители столбцов и строк, кодировки, и т. д., должны быть определены в конструкторе класса. Интерфейс `IOutputter` также должен содержать определение для переопределения `void Output`. Можно задать необязательный атрибут `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` для атомарной обработки файла (подробнее см. ниже).

```c#
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
* Метод `Close` можно переопределить, чтобы выходить из ресурсозатратных состояний или узнавать время записи последней строки.

Атрибут **SqlUserDefinedAggregate** указывает, что этот тип должен быть зарегистрирован как пользовательское средство вывода. Этот класс не наследуется.

SqlUserDefinedOutputter — это необязательный атрибут для определения пользовательских средств вывода. Он используется для определения свойства AtomicFileProcessing.

* bool AtomicFileProcessing.   

* **true** — указывает, что это средство вывода требует предоставлять атомарные выходные файлы (JSON, XML, ...).
* **false** — указывает, что это средство вывода умеет работать с разделенными и (или) распределенными файлами (CSV, SEQ,...).

Основные объекты, поддерживающие программирование, — это *row* (строка) и *output* (вывод). Объект *row* используется для последовательного перебора входных данных через интерфейс `IRow`, а объект *output* — для отправки выходных данных в целевой файл.

Обращение к выходным данным выполняется через интерфейс `IRow`. Выходные данные передаются построчно.

Отдельные значения перебираются с помощью метода Get интерфейса IRow.

```c#
    row.Get<string>("column_name")
```

Имена отдельных столбцов можно узнать, вызвав метод `row.Schema`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Такой подход позволяет создавать гибкие средства вывода для любой схемы метаданных.

Выходные данные записываются в файл с помощью метода `System.IO.StreamWriter`, если параметр потока имеет значение `output.BaseStrea` в структуре `IUnstructuredWriter output`.

Важное примечание. Чтобы буфер данных передавался в файл после каждой итерации строки, для объекта `StreamWriter` должен быть установлен атрибут Disposable со стандартным значением enabled и ключевым словом *using*:

```c#
    using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
            {
        …
            }
```

В противном случае нужно явно вызывать метод Flush() после каждой итерации, как показано далее в этом примере:

### <a name="setting-header-and-footer-for-user-defined-outputter"></a>Установка верхнего и нижнего колонтитулов для пользовательского средства вывода
Чтобы задать верхний колонтитул, используйте последовательность однократного выполнения.

```c#
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

Для нижнего колонтитула используйте ссылку на экземпляр класса `System.IO.Stream` объекта (`output.BaseStream`) и выводите нижний колонтитул в метода Close() интерфейса `IOutputter` (см. пример ниже).

Пример пользовательского средства вывода:

```c#
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

            // Close method is used to write footer to file - executed only once after all rows
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

                // This is data independent header - HTML table definition
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

                // Header row output - runs only once
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
                        // Data type enumeration - require to match the distinct list of types form OUTPUT statement
                        switch (col.Type.Name.ToString().ToLower())
                        {
                            case "string": val = row.Get<string>(col.Name).ToString(); break;
                            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
                            default: break;
                        }
                    }
                    // Hnadling NULL values - keeping them empty
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

И соответствующий основной скрипт U-SQL:

```sql
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

OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Это средство вывода для формата HTML. Оно создает HTML-файл с данными из таблицы.

### <a name="calling-outputter-from-u-sql-base-script"></a>Вызов средства вывода из основного скрипта U-SQL
Чтобы вызвать пользовательское средство вывода из основного скрипта U-SQL, нужно создать новый экземпляр объекта для средства вывода.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Чтобы не создавать экземпляр объекта в основном скрипте, мы можем создать оболочку функции. Фрагмент из примера выше:

```c#
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

```sql
OUTPUT @rs0 TO @output_file USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="user-defined-processor"></a>Пользовательские средства обработки
Пользовательское средство обработки — это тип определяемого пользователем объекта U-SQL, который позволяет обрабатывать входящие строки, применяя к ним средства программирования. Пользовательское средство обработки может объединять столбцы, изменять значения или добавлять новые столбцы. По сути оно позволяет получить нужные элементы данных, обрабатывая наборы строк.

Чтобы определить пользовательское средство обработки, нужно создать интерфейс `IProcessor` с атрибутом `SqlUserDefinedProcessor`, который является необязательным для пользовательского средства обработки.

Этот интерфейс должен содержать определение, переопределяющее интерфейс объекта `IRow` для набора строк:

```c#
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

Атрибут SqlUserDefinedProcessor является **необязательным** для пользовательского средства обработки.

Основные объекты, поддерживающие программирование, — это *input* (ввод) и *output* (вывод). Объект Input используется для перечисления входных столбцов, а объект Output — для вывода данных, полученных в результате действий по обработке.

Для перечисления входных столбцов мы используем метод `input.Get`:

```c#
    string column_name = input.Get<string>("column_name");
```

Параметр, передаваемый методу `input.Get`, представляет собой столбец, представленный в предложении `PRODUCE` для инструкции ` PROCESS` в основном скрипте U-SQL. Здесь необходимо использовать правильный тип данных.

Для вывода вызовите метод output.Set

Важно понимать, что пользовательские средства обработки будут возвращать только те выходные столбцы и значения, которые определены в вызове метода output.Set.

```c#
    output.Set<string>("mycolumn", mycolumn);
```

Фактический вывод данных из средства обработки инициируется вызовом метода `return output.AsReadOnly()`.

Пример использования средства обработки:

```c#
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

В этом сценарии использования средство обработки создает новый столбец full_description, в котором объединяет сведения из уже существующих столбцов user (в верхнем регистре) и des. Также оно заново создает идентификатор guid и возвращает исходное и новое значения идентификатора.

Как видно выше, методы C# можно вызывать при обращении к output.Set.

Основной скрипт U-SQL, который использует пользовательское средство обработки:

```sql
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

## <a name="user-defined-applier"></a>Пользовательские средства применения
Пользовательские средства применения в U-SQL позволяют вызывать пользовательскую функцию C# для каждой строки, возвращаемой выражением запроса к внешней таблице. Входные данные справа оцениваются для каждой строки из входных данных слева, а затем результат объединяется в итоговых выходных данных. Список столбцов, созданных оператором APPLY, — это комбинация набора столбцов из входных данных слева и справа.

Пользовательские средства применения вызываются как часть выражения U-SQL SELECT.

Типичный вызов пользовательских средств применения выглядит так:

```sql
    SELECT …
    FROM …
    CROSS APPLY
    new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Сведения об использовании средств применения в выражении SELECT см. в статье [U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx) (Получение сведений с помощью U-SQL SELECT из CROSS APPLY и OUTER APPLY).

Определение базового класса для пользовательского средства применения:

```c#
    public abstract class IApplier : IUserDefinedOperator
    {
        protected IApplier();

        public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
    }
```

Чтобы задать пользовательское средство применения, нам нужен интерфейс `IApplier` с атрибутом [`SqlUserDefinedCombiner`],который не является обязательным для определения пользовательских средств применения.

```c#
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

* Операция применения вызывается для каждой строки внешней таблицы. Возвращает выходной набор строк `IUpdatableRow`.
* Класс Constructor используется для передачи параметров в пользовательское средство применения.

**SqlUserDefinedApplier** указывает, что тип должен быть зарегистрирован как пользовательское средство применения. Этот класс не наследуется.

Атрибут SqlUserDefinedApplier является **необязательным** для определения пользовательского средства применения.


Основные объекты, поддерживающие программирование:

```c#
        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Входной набор строк передается в виде ввода `IRow`. Выходные строки создаются как интерфейс вывода `IUpdatableRow`.

Имена отдельных столбцов можно узнать, вызвав метод `IRow` объекта Schema.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Для получения фактических значений данных из входящего `IRow` используйте метод Get() интерфейса `IRow`:

```c#
    mycolumn = row.Get<int>("mycolumn")
```

Или используйте имена столбцов схемы:

```c#
    row.Get<int>(row.Schema[0].Name)
```

Выходные значения следует передавать в выход `IUpdatableRow`:

```c#
    output.Set<int>("mycolumn", mycolumn)
```

Важно понимать, что пользовательские средства применения будут возвращать только те выходные столбцы и значения, которые определены в вызове метода output.Set.

Фактический вывод данных из средства применения инициируется вызовом метода `yield return output.AsReadOnly()`.

Пользовательские средства применения позволяют передавать параметры в конструктор. Средство применения может возвращать переменное число столбцов, которое должно быть определено при вызове средства применения из основного скрипта U-SQL.

```c#
  new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Приведен пример использования пользовательского средства применения:

```c#
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

```sql
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

В этом сценарии пользовательское средство применения действует как анализатор значений с разделителями-запятыми для набора свойств автомобильного парка. Строки входного файла будут выглядеть так:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Это стандартный TSV-файл со значениями с разделением знаками табуляции, в котором столбец Properties содержит такие свойства автомобилей, как производитель, модель и т. д. Эти свойства нужно проанализировать и преобразовать в столбцы таблицы. Представленное средство применения позволяет создавать динамически изменяемое количество свойств в результирующем наборе строк, которое определяется переданным параметром. Можно создавать столбцы для всех свойств или только для определенного набора.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Пользовательское средство применения может вызываться как новый экземпляр средства применения:

```c#
    CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Или с использованием метода фабрики оболочки:

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="user-defined-combiner"></a>Пользовательские средства объединения
Пользовательское средство объединения позволяет объединить строки из левого и правого наборов строк, используя настраиваемую логику объединения. Пользовательское средство объединения используется с выражением COMBINE.

Средство объединения вызывается из выражения COMBINE, которое предоставляет необходимую информацию о входных наборах строк, столбцах для группирования, схеме ожидаемого результата, а также дополнительные сведения.

Чтобы вызвать средство объединения из основного скрипта U-SQL, используйте следующий синтаксис:

```sql
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

Чтобы задать пользовательское средство объединения, нам нужен интерфейс `ICombiner` с атрибутом [`SqlUserDefinedCombiner`],который не является обязательным для определения пользовательских средств объединения.

Определение базового класса `ICombiner`:

```c#
    public abstract class ICombiner : IUserDefinedOperator
    {
        protected ICombiner();
        public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
               IUpdatableRow output);
        public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
               IUpdatableRow output);
    }
```

Пользовательская реализация интерфейса `ICombiner` должна содержать определение, переопределяющее метод `IEnumerable<IRow>` Combine.

```c#
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

SqlUserDefinedCombiner — это необязательный атрибут для определения пользовательских средств объединения. Он определяет свойство режима для средства объединения.

Режимы CombinerMode

Перечисление CombinerMode может принимать следующие значения:

* Full (0) — каждая строка выходных данных может зависеть от всех входных строк слева и справа с одинаковым значением ключа.

* Left (1) — каждая строка выходных данных зависит от одной входной строки из левого входа (и потенциально от всех строк из правого входа с тем же значением ключа).

* Right (2) — каждая строка выходных данных зависит от одной входной строки из правого входа (и потенциально от всех строк из левого входа с тем же значением ключа).

* Inner (3) — каждая строка выходных данных зависит от одной строки из левого входа и одной строки из правого входа с тем же значением ключа.

Пример: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Основные объекты, поддерживающие программирование:

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Входные наборы строк передаются в виде левого (*left*) и правого (*right*) интерфейсов типа `IRowset`. Для обработки нужно выполнить перечисление обоих наборов строк. Каждый интерфейс можно перечислить только один раз, поэтому для повторной обработки их нужно перечислять с использованием кэша.

Для целей кэширования можно создать структуру в памяти типа List\<T\> с помощью запроса LINQ. Например, List<`IRow`>. Также для перечисления можно использовать анонимные типы данных.

Сведения о запросах LINQ см. в статье [Introduction to LINQ Queries (C#)](https://msdn.microsoft.com/library/bb397906.aspx) (Введение в запросы LINQ (C#)), а описание интерфейса IEnumerable\<T\> — в статье [IEnumerable\<T\> Interface](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) (Интерфейс IEnumerable<T>).

Для получения фактических значений данных из входящего `IRowset` используйте метод Get() интерфейса `IRow`:

```c#
    mycolumn = row.Get<int>("mycolumn")
```

Имена отдельных столбцов можно узнать, вызвав метод `IRow` объекта Schema.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Или используйте имена столбцов схемы:

```
    c# row.Get<int>(row.Schema[0].Name)
```

В общем виде перечисление с помощью LINQ используется так:

```c#
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Перечислив оба набора строк, мы будем в цикле перебирать все строки, выбирая для каждой строки из левого набора все строки, которые удовлетворяют условию, заданному для средства объединения.

Выходные значения следует передавать в выход `IUpdatableRow`:

```c#
    output.Set<int>("mycolumn", mycolumn)
```

Фактический вывод данных из средства применения инициируется вызовом метода `yield return output.AsReadOnly()`.

Ниже приведен пример средства объединения

```c#
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

Вот основной скрипт U-SQL: Можно сравнить логику обычной инструкции JOIN и средства объединения:

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

Пользовательское средство объединения может вызываться как новый экземпляр средства объединения:

```c#
    USING new MyNameSpace.MyCombiner();
```


Или с использованием метода фабрики оболочки:

```c#
    USING MyNameSpace.MyCombiner();
```

## <a name="user-defined-reducer"></a>Пользовательские средства редукции
U-SQL позволяет создать пользовательское средство редукции для набора строк на языке C#, используя платформу расширения для определяемых пользователем операторов и реализацию интерфейса IReducer.

Определяемые пользователем редукторы можно использовать для исключения ненужных строк в процессе извлечения (импорта) данных. Также с помощью этого средства можно обрабатывать и оценивать строки и столбцы, чтобы выбрать извлекаемые строки с использованием программируемой логики.

Чтобы определить класс пользовательского средства редукции, нужно создать интерфейс `IReducer` с необязательным атрибутом `SqlUserDefinedReducer`.

Этот интерфейс класса должен содержать определение, переопределяющее интерфейс объекта `IEnumerable` для набора строк:

```c#
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

SqlUserDefinedReducer — это необязательный атрибут для определения пользовательских средств редукции. Он используется для определения свойства IsRecursive:

* bool IsRecursive.    
* **true** — указывает, что это средство редукции является идемпотентным.

Основные объекты, поддерживающие программирование, — это *input* (ввод) и *output* (вывод). Объект Input используется для перечисления входных строк, а объект Output — для вывода строк, полученных в результате действий по редукции.

Для перечисления входных строк мы используем метод `Row.Get`:

```c#
            foreach (IRow row in input.Rows)
            {
                        row.Get<string>("mycolumn");
            }
```

Необходимо учитывать следующие важные замечания. Параметр, передаваемый методу `Row.Get`, представляет столбец, представленный в классе `PRODUCE` инструкции `REDUCE` в основном скрипте U-SQL. Здесь необходимо использовать правильный тип данных.

Для вывода вызовите метод output.Set

Важно понимать, что пользовательские средства редукции будут возвращать только те выходные значения, которые определены в вызове метода output.Set.

```c#
    output.Set<string>("mycolumn", guid);
```

Фактический вывод данных из средства редукции инициируется вызовом метода `yield return output.AsReadOnly()`.

Ниже приведен пример средства редукции

```c#
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

В этом сценарии использования средство редукции пропускает строки с пустым именем пользователя. Для каждой строки в наборе строк считывается каждый обязательный столбец, затем оценивается длина имени пользователя. Текущую строку средство передает на выход, только если длина имени пользователя превышает 0.

Основной скрипт U-SQL, который использует пользовательское средство редукции:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

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

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```



<!--HONumber=Nov16_HO3-->


