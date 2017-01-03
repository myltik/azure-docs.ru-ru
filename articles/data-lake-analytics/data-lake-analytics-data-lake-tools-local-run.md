---
title: "Тестирование и отладка заданий U-SQL с помощью локального выполнения и пакета SDK U-SQL для Azure Data Lake | Документация Майкрософт"
description: "Узнайте, как с помощью средств Azure Data Lake для Visual Studio и пакета SDK U-SQL для Azure Data Lake выполнять тестирование и отладку заданий U-SQL на локальной рабочей станции."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 9d088a3afd8830c80462099cc869f1493d09fb41
ms.openlocfilehash: e0385315434ac7a10e121f88469aaaec45160b86


---
# <a name="test-and-debug-u-sql-jobs-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Тестирование и отладка заданий U-SQL с помощью локального выполнения и пакета SDK U-SQL для Azure Data Lake

Узнайте, как с помощью средств Azure Data Lake для Visual Studio и пакета SDK U-SQL для Azure Data Lake выполнять тестирование и отладку заданий U-SQL на локальной рабочей станции.  Эти два средства для локального выполнения позволяют запускать задания U-SQL на рабочей станции так же, как и в службе Azure Data Lake. Эти компоненты помогут вам быстрее выполнять тестирование и отладку заданий U-SQL.

Предварительные требования: 

- Учетная запись Azure Data Lake Analytics. См. статью о [начале работы с Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md). 
- Средства Azure Data Lake для Visual Studio.  См. [руководство по разработке скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). 
- Опыт разработки скриптов U-SQL. См. статью о [начале работы с Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md). 


## <a name="understand-data-root-and-file-path"></a>Понятие корневой папки данных и пути к файлу

Для локального выполнения и использования пакетов SDK U-SQL вам потребуется настроить корневую папку данных. Корневая папка данных служит "локальным хранилищем" для локальной учетной записи среды вычислений. Она действует так же, как учетная запись Data Lake Store в учетной записи Azure Data Lake Analytics. Переход на другую корневую папку данных аналогичен переходу на другую учетную запись хранения. Если вам нужен доступ к данным, которые совместно используются различными корневыми папками данных, следует использовать в скриптах абсолютные пути или создать в корневой папке данных символические ссылки файловой системы (например, с помощью команды mklink для файловой системы NTFS), указывающие на совместно используемые данные. 

Корневая папка данных используется в следующих целях.

- Хранение метаданных, включая базы данных, таблицы, функции с табличным значением, сборки и т. д.
- Поиск путей ввода-вывода, которые определяются как относительные пути в U-SQL. Использование относительных путей упрощает развертывание проектов U-SQL в Azure.

В скриптах U-SQL можно использовать как относительные, так и абсолютные локальные пути. При этом относительный путь задается относительно пути к выбранной корневой папке данных. Мы рекомендуем использовать в качестве разделителя пути символ "/", чтобы обеспечить совместимость скриптов с выполнением на стороне сервера. Ниже приведены некоторые примеры относительных путей и их абсолютные эквиваленты. В этих примерах предполагается, что мы используем корневую папку данных C:\LocalRunDataRoot.

|Относительный путь|Абсолютный путь|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Локальное выполнение из Visual Studio

Средства Data Lake для Visual Studio предоставляют возможность локального выполнения U-SQL в Visual Studio. Это позволит вам:

- локально запускать скрипты U-SQL вместе со сборками C#;
- локально выполнять отладку сборок C#;
- создавать, просматривать и удалять каталоги U-SQL (локальные базы данных, сборки, схемы и таблицы) с помощью обозревателя сервера. Локальный каталог также можно найти с помощью обозревателя сервера.

    ![Локальный каталог для локального выполнения средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Установщик средств Data Lake создает папку C:\LocalRunRoot, которая по умолчанию используется в качестве корневой папки данных. По умолчанию для локального выполнения используется коэффициент параллелизма 1. 

### <a name="to-configure-local-run-in-visual-studio"></a>Настройка локального выполнения в Visual Studio

1. Откройте Visual Studio.
2. Откройте **обозреватель сервера**.
3. Разверните узлы **Azure**, **Data Lake Analytics**.
4. Откройте меню **Data Lake** и выберите пункт **Параметры и настройки**. 
5. В дереве слева разверните узел **Azure Data Lake**, а затем — **Общие**.

    ![Настройка параметров для локального выполнения средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Для локального выполнения требуется создать проект U-SQL для Visual Studio. Эта часть выполняется не так, как при запуске скриптов U-SQL в Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Локальное выполнение скрипта U-SQL
1. В Visual Studio откройте нужный проект U-SQL.   
2. В обозревателе решений щелкните правой кнопкой мыши скрипт U-SQL и выберите команду **Submit Script** (Отправить скрипт). Выберите (local) в качестве учетной записи аналитики для локального выполнения скрипта.
3. Также можно щелкнуть учетную запись (local) в верхней части окна скрипта, а затем щелкнуть **Отправить** (или использовать сочетание клавиш **CTRL+F5**).

    ![Отправка заданий для локального выполнения средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-data-lake-u-sql-sdk"></a>Использование локального выполнения из пакета SDK U-SQL для Data Lake
 
Для локального выполнения скриптов U-SQL можно использовать не только Visual Studio, но и пакет SDK U-SQL для Azure Data Lake. В последнем случае используется интерфейс командной строки или программные интерфейсы, что позволяет масштабировать сценарии локального тестирования U-SQL.

### <a name="install-the-sdk"></a>Установка пакета SDK

Для пакета SDK U-SQL для Data Lake требуются следующие зависимости:

- [Microsoft .NET Framework 4.6 или более поздней версии](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 и пакет SDK Windows 10.0.10240.0 или более поздней версии. Их можно получить двумя способами.

    - Установите Visual Studio ([выпуск Visual Studio Community](https://developer.microsoft.com/downloads/vs-thankyou)). В папке Program Files должна существовать папка \Windows Kits\10, например C:\Program Files (x86) \Windows Kits\10\". Кроме того, в папке \Windows Kits\10\Lib должна находиться версия пакета SDK для Windows 10. Если вы не найдете у себя эти папки, переустановите Visual Studio и обязательно выберите с помощью флажка пакет SDK для Windows 10 в процессе установки. Это позволит скрипту локального компилятора U-SQL автоматически найти эти зависимости.

    ![Пакет SDK Windows 10 для локального выполнения средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)
 
    - Также вы можете установить [средства Data Lake для Visual Studio](http://aka.ms/adltoolsvs). Предварительно упакованные файлы VC++ и SDK для Windows можно найти в каталоге C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. В этом случае локальный компилятор U-SQL не сможет найти зависимости автоматически, и вам потребуется указать путь к CppSDK. Можно скопировать файлы в другое место или просто использовать стандартный путь. Затем следует указать используемый каталог либо в переменой среды SCOPE_CPP_SDK, либо в аргументе -CppSDK в командной строке вспомогательного приложения для локального выполнения. 

После установки пакета SDK необходимо выполнить следующие действия по настройке.

- Настройка переменной среды **SCOPE_CPP_SDK**

    Если для получения Microsoft Visual C++ и пакета SDK для Windows вы установили средства Data Lake для Visual Studio, убедитесь в наличии следующей папки:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Определите новую переменную среды с именем SCOPE_CPP_SDK, которая будет указывать на этот каталог. Также можно скопировать эту папку в другое расположение и указать новый путь к ней в переменной среды SCOPE_CPP_SDK.

    Кроме настройки переменной среды, вы можете использовать аргумент -CppSDK в командной строке. Этот аргумент переопределяет используемую по умолчанию переменную среды CppSDK. 

- Настройка переменной среды **LOCALRUN_DATAROOT**

    Определите новую переменную среды с именем LOCALRUN_DATAROOT, которая будет указывать на корневую папку данных. 

    Кроме настройки переменной среды, вы можете использовать аргумент -DataRoot в командной строке, который указывает на корневую папку данных. Этот аргумент переопределяет используемую по умолчанию переменную среды, задающую путь к корневой папке данных. Этот аргумент необходимо добавлять во все выполняемые командные строки, чтобы для всех операций использовалась одна и та же новая корневая папка данных. 

### <a name="using-the-sdk-from-command-line"></a>Использование пакета SDK из командной строки

#### <a name="the-command-line-interface-of-the-helper-application"></a>Интерфейс командной строки вспомогательного приложения

Файл LocalRunHelper.exe из пакета SDK содержит вспомогательное приложение командной строки, которое предоставляет интерфейсы для большинства распространенных функций локального выполнения. Обратите внимание, что в командах и параметрах аргументов учитывается регистр. Для вызова вспомогательного приложения выполните следующую команду:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Если запустить LocalRunHelper.exe без аргументов или с параметром **help**, отобразится справочная информация:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Эта справочная информация содержит следующие элементы. 

-  **Command** определяет имя выполняемой команды.  
-  **Required Argument** перечисляет обязательные аргументы.  
-  **Optional Argument** перечисляет необязательные аргументы и приводит для них значения по умолчанию.  Если необязательный аргумент имеет тип логического значения, он не принимает дополнительные параметры, и тогда его наличие определяет поведение, противоположное значению по умолчанию.

#### <a name="return-value-and-logging"></a>Возвращаемое значение и ведение журнала

Вспомогательное приложение возвращает значение **0** в случае успешного выполнения или **-1** в случае сбоя. По умолчанию вспомогательное приложение выводит все служебные сообщения в текущую консоль.  Однако большинство команд поддерживают необязательный аргумент **-MessageOut path_to_log_file**, который позволяет перенаправлять выходные данные в файл журнала.


### <a name="the-sdk-usage-samples"></a>Примеры использования пакета SDK

#### <a name="compile-and-run"></a>Компиляция и запуск

Команда run компилирует скрипт и выполняет программу, полученную в результате компиляции. Эта команда принимает все аргументы командной строки, которые доступны для команд compile или run.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Пример:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Операции компиляции (compile) и выполнения (run) можно также выполнять по отдельности. 

#### <a name="compile-u-sql-script"></a>Компиляция скрипта U-SQL

Команда compile позволяет выполнить компиляцию скрипта U-SQL в исполняемые файлы. 

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Ниже представлены необязательные аргументы для команды компиляции. Дополнительные сведения о рабочем каталоге (-WorkDir) см. в приложении.

|Аргумент|Описание|
|--------|-----------|
|-CppSDK [значение по умолчанию "]|Каталог CppSDK.|
|-DataRoot [значение по умолчанию "]|Корневая папка для хранения данных и метаданных. По умолчанию используется значение из переменной среды LOCALRUN_DATAROOT.|
|-MessageOut [значение по умолчанию "]|Сохранение в файл всех сообщений, предназначенных для вывода на консоль.|
|-Shallow [значение по умолчанию False]|Неполная компиляция. Выполняет только проверку синтаксиса и прерывает работу.|
|-WorkDir [значение по умолчанию D:\localrun\t\ScopeWorkDir]|Задает каталог для работы и выходных данных компилятора. Использование рабочего каталога описано в соответствующем разделе приложения.|

Необязательные аргументы для сборок и кода программной части.

|Аргумент|Описание|
|--------|-----------|
|-CodeBehind [значение по умолчанию False]|Указывает, что скрипт содержит файл кода программной части (CS-файл), который будет автоматически скомпилирован и зарегистрирован как определяемый пользователем объект.|
|-References [значение по умолчанию "]|Список путей к дополнительным справочным сборкам или файлам данных кода программной части с разделителем ";".|
|-UseDatabase [значение по умолчанию master]|База данных, в которой нужно регистрировать временную сборку кода программной части.|
|-UdoRedirect [значение по умолчанию False]|Создает конфигурацию перенаправления для сборки определяемого пользователем объекта, в соответствии с которой среда выполнения .NET при вызове определяемого пользователем объекта будет в первую очередь искать зависимые сборки в каталоге выходных данных компиляции.|

Вот несколько примеров использования команды.

Компиляция скрипта U-SQL.

    LocalRunHelper compile -Script d:\test\test1.usql

Компиляция скрипта U-SQL с определенной корневой папкой данных. Обратите внимание, что этот аргумент переопределяет заданную переменную среды.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Компиляция скрипта U-SQL с определенным рабочим каталогом и ссылками на сборку и базу данных.

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-result"></a>Выполнение скомпилированного результата

Команда execute используется для выполнения скомпилированного результата.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Необязательные аргументы для этой команды.

|Аргумент|Описание|
|--------|-----------|
|-DataRoot [значение по умолчанию "]|Корневая папка для обработки метаданных. По умолчанию используется значение из переменной среды LOCALRUN_DATAROOT.|
|-MessageOut [значение по умолчанию "]|Сохранение в файл всех сообщений, предназначенных для вывода на консоль.|
|-Parallel [значение по умолчанию 1]|Выполнение созданных шагов локального выполнения на указанном уровне параллелизма.|
|-Verbose [значение по умолчанию False]|Отображение подробных выходных данных среды выполнения.|

Пример использования этой команды.

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="using-the-sdk-with-programming-interface"></a>Использование пакета SDK с программным интерфейсом

Все программные интерфейсы размещаются в сборке Microsoft.Analytics.LocalRun. Они позволяют объединить функциональность пакетов SDK U-SQL и тестовой платформы C#, чтобы масштабировать локальное тестирование скрипта U-SQL. В ближайшее время ожидается новый документ по этой теме. Дополнительные сведения об интерфейсах см. в приложении.

## <a name="appendix"></a>Приложение

### <a name="working-directory"></a>Рабочий каталог

Если скрипт U-SQL выполняется локально, рабочий каталог создается во время компиляции.  В него записываются выходные данные процесса компиляции, а также здесь создается теневая копия всех файлов среды выполнения, которые требуются для локального выполнения.   Если в командной строке не указан аргумент **-WorkDir**, по умолчанию создается рабочий каталог ScopeWorkDir в текущем каталоге. Ниже представлены файлы, которые размещаются в рабочем каталоге.

|Каталог/файл|Определение|Описание|
|--------------|----------|-----------|
|ScopeWorkDir|Рабочий каталог|корневая папка|
|C6A101DDCB470506|Хэш-строка версии среды выполнения|Теневая копия файлов среды выполнения, необходимых для локального выполнения|
|\.\Script_66AE4909AA0ED06C|Имя скрипта и хэш-строка пути к скрипту|Выходные данные компиляции и ведение журнала шагов выполнения|
|\.\.\\_\_script\_\_.abr|Выходные данные компилятора|Файл Algebra|
|\.\.\\_\_ScopeCodeGen\_\_.*|Выходные данные компилятора|Созданный управляемый код|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Выходные данные компилятора|Созданный собственный код|
|\.\.\referenced_assemblies|Ссылка на сборку|Файлы ссылок на сборку|
|\.\.\deployed_resources|Развертывание ресурсов|Файлы развертывания ресурсов|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Журнал выполнения|Журнал шагов выполнения|

### <a name="programming-interfaces-for-azure-data-lake-u-sql-sdk"></a>Программные интерфейсы для пакета SDK U-SQL для Azure Data Lake

Программные интерфейсы находятся в сборке Microsoft.Analytics.LocalRun.

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Класс параметров конфигурации компиляции

**Конструктор**

public Configuration(string rootPath)

|Параметр|Тип|Описание|
|---------|----|-----------|
|rootPath|System.String|Путь к текущему каталогу рабочего контекста. Если каталог WorkingDirectory не задан, по умолчанию в качестве рабочего каталога используется rootPath + ScopeWorkDir.|

**Свойства**

|Имя|Описание|
|----|-----------|
|CppSDK|Путь размещения пакета SDK Cpp. По умолчанию используется системная конфигурация по умолчанию |
|DataDirectory|Путь для сохранения таблиц, сборок и данных ввода-вывода. По умолчанию используется ScopeWorkDir\DataDir |
|GenerateUdoRedirect|Указывает, нужно ли создавать конфигурацию, переопределяющую пути перенаправления для загрузки сборки|
|WorkingDirectory|Рабочий каталог компилятора. Если параметр не задан, по умолчанию используется значение ScopeWorkDir|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
Класс локального компилятора U-SQL

**Конструктор**

public LocalCompiler(Configuration configuration)

|Параметр|Тип|Описание|
|---------|----|-----------|
|Конфигурация|Microsoft.Analytics.LocalRun.Configuration||

**Метод**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Параметр|Тип|Описание|
|---------|----|-----------|
|script|System.String|Строка ввода скрипта|
|filePath|System.String|Путь к файлу скрипта. Если указано значение NULL, используется значение по умолчанию|
|shallow|System.Boolean|Определяет режим компиляции: неполная (только синтаксическая проверка) или полная|
|result|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Подробные результаты компиляции|
|Возвращаемое значение|System.Boolean|Значение true: нет серьезных ошибок при компиляции. <br>Значение false: есть серьезные ошибки при компиляции.|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Класс локального средства выполнения U-SQL

**Конструктор**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Параметр|Тип|Описание|
|---------|----|-----------|
|algebraFilePath|System.String|Путь к файлу Algebra|
|dataRoot|System.String|Путь к корневой папке данных|
|postMessage (необязательно)|System.Action<String>|Обработчик ведения журнала для отслеживания хода выполнения|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Параметр|Тип|Описание|
|---------|----|-----------|
|algebraFilePath|System.String|Путь к файлу Algebra|
|dataRoot|System.String|Путь к корневой папке данных|
|cachePath|System.String|Путь к каталогу с результатом компиляции. Если указано значение NULL, по умолчанию используется путь, по которому расположен файл Algebra|
|runtimePath|System.String|Путь к каталогу теневого копирования среды выполнения. Если указано значение NULL, по умолчанию используется родительский каталог cachePath|
|tempPath|System.String|Путь временного хранилища. Только для внутреннего использования. Задано значение NULL|
|logPath|System.String|Путь, по которому будут сохраняться журналы выполнения. Если указано значение NULL, по умолчанию используется значение cachePath|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|Обработчик уведомлений о событиях изменения состояния выполнения|
|eventContext|System. Объект|Контекст для обработчика событий|
|postMessage (необязательно)|System.Action<String>|Обработчик ведения журнала для отслеживания хода выполнения|

**Свойства**

|Имя|Описание|
|----|-----------|
|AlgebraPath|Путь к файлу Algebra|
|CachePath|Путь к кэшу результата работы компилятора, где расположены созданные двоичные файлы|
|CompletedSteps|Число выполненных шагов|
|DataRoot|Корневая папка для метаданных|
|LastErrorMessage|(Наследуется от ExecutionStatusBase)|
|LogPath|Место хранения файла журнала. Если каталог не существует, он будет создан. Ранее созданные каталоги журнала не очищаются.|
|OutputHeader|Заголовок для схемы дампа в текстовых выходных данных|
|Параллелизм|Коэффициент параллелизма. По умолчанию для обработчиков логики используется значение 1. Если изменить этот параметр после начала выполнения, создается исключение|
|Ход выполнения|Ход выполнения в процентах готовности (от 0 до 100)|
|RuntimePath|Путь размещения файлов среды выполнения. Если используется теневое копирование на уровне компилятора, этот каталог должен размещаться на один уровень выше значения CachePath|
|Состояние|Состояние выполнения <br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized — инициализировано <br>Running — выполняется. WaitOne только проверяет события в таком состоянии <br>Success — успешно выполнено <br>Error — сбой <br>}|
|TotalSteps|Общее число шагов, которые должны быть выполнены. Допустимое значение будет доступно только после построения DAG вершины|
|Подробная информация|Подробные сведения во время выполнения|

**Метод**

|Метод|Описание|
|------|-----------|
|Cancel()|Отмена выполняемого элемента алгебры <br>Возвращаемое значение <br>Тип: логический <br>Значение false: не удалось отменить из-за ошибки. Подробные сведения см. в параметре LastErrorMessage|
|Start()|Запуск вычислений элемента алгебры <br>Возвращаемое значение <br>Тип: логический <br>Значение false: не удалось запустить из-за ошибки. Подробные сведения см. в параметре LastErrorMessage|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Ожидание завершения, подробнее см. в WaitHandle.WaitOne|
|Dispose()||


## <a name="see-also"></a>См. также

* Общие сведения об Azure Data Lake Analytics см. в [этой статье](data-lake-analytics-overview.md).
* Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
* Сведения о том, как записывать диагностические данные в журнал, см. в статье [Доступ к журналам диагностики для Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Для просмотра сведений о заданиях см. статью [Use Job Browser and Job View for Azure Data Lake Analytics jobs](data-lake-analytics-data-lake-tools-view-jobs.md) (Использование обозревателя заданий и представления заданий для заданий Azure Data Lake Analytics).
* Дополнительные сведения см. в статье [Использование представления выполнения вершин в инструментах Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).


<!--HONumber=Nov16_HO4-->


