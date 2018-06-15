---
title: Использование C# с Hive и Pig в Hadoop в HDInsight — Azure | Документы Майкрософт
description: Узнайте, как использовать определяемые пользователем функции C# при потоковой передаче Hive и Pig в Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: b776b214b9d46293a3ab97ad226dffd3107f3430
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31404041"
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Использование определяемых пользователем функций C# при потоковой передаче Hive и Pig в Hadoop HDInsight.

Узнайте, как использовать определяемые пользователем функции C# с Apache Hive и Pig в HDInsight.

> [!IMPORTANT]
> Инструкции, описанные в этом документе, можно применять к кластерам HDInsight под управлением Linux или Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)

Hive и Pig могут передавать данные во внешние приложения для обработки. Этот процесс называется _потоковой передачей_. При использовании приложения .NET данные передаются в него через поток STDIN, после чего приложение возвращает результаты через поток STDOUT. Для чтения и записи с помощью потоков STDIN и STDOUT можно использовать `Console.ReadLine()` и `Console.WriteLine()` из консольного приложения.

## <a name="prerequisites"></a>предварительным требованиям

* Опыт написания и выполнения сборки кода C#, предназначенного для платформы .NET Framework 4.5.

    * Используйте любую интегрированную среду разработки. Мы рекомендуем использовать [Visual Studio](https://www.visualstudio.com/vs) 2015, Visual Studio 2017 или [Visual Studio Code](https://code.visualstudio.com/). В этом руководстве используется Visual Studio 2017.

* Способ передачи EXE-файлов в кластер и выполнения заданий Pig и Hive. Мы рекомендуем использовать средства Data Lake для Visual Studio, Azure PowerShell и Azure CLI. В этом документе для передачи файлов и выполнения примера запроса Hive используются средства Data Lake для Visual Studio.

    Сведения о других способах выполнения запросов Hive и заданий Pig см. в следующих документах:

    * [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md)

    * [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* Hadoop в кластере HDInsight. Дополнительные сведения о создании кластера см. в статье [Создание кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET в HDInsight

* В кластерах __HDInsight под управлением Linux__ для запуска приложений .NET используется [Mono (https://mono-project.com)](https://mono-project.com). Mono версии 4.2.1 входит в состав HDInsight версии 3.6.

    Дополнительные сведения о совместимости Mono с различными версиями платформы .NET Framework см. в разделе [Compatibility](http://www.mono-project.com/docs/about-mono/compatibility/) (Совместимость).

    Чтобы использовать определенную версию Mono, см. статью об [установке или обновлении Mono](../hdinsight-hadoop-install-mono.md).

* В кластерах __HDInsight под управлением Windows__ для запуска приложений .NET используется среда CLR для Microsoft .NET.

Дополнительные сведения о версиях платформы .NET Framework и Mono, которые входят в состав различных версий HDInsight, см. в разделе [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)

## <a name="create-the-c-projects"></a>Создание проектов C\#

### <a name="hive-udf"></a>Определяемая пользователем функция Hive

1. Откройте Visual Studio и создайте решение. Выберите для проекта тип **Консольное приложение (.NET Framework)** и назовите новый проект **HiveCSharp**.

    > [!IMPORTANT]
    > Если используется кластер HDInsight под управлением Linux, выберите __.NET Framework 4.5__. Дополнительные сведения о совместимости Mono с различными версиями платформы .NET Framework см. в разделе [Compatibility](http://www.mono-project.com/docs/about-mono/compatibility/) (Совместимость).

2. Замените содержимое файла **Program.cs** кодом, приведенным ниже.

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Создайте проект.

### <a name="pig-udf"></a>Определяемая пользователем функция Pig

1. Откройте Visual Studio и создайте решение. Выберите для проекта тип **Консольное приложение** и назовите его **PigUDF**.

2. Замените содержимое файла **Program.cs** следующим кодом:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Этот код анализирует строки, отправленные из Pig, и переформатирует строки, которые начинаются с `java.lang.Exception`.

3. Сохраните **Program.cs**, а затем создайте проект.

## <a name="upload-to-storage"></a>Отправка в хранилище

1. В Visual Studio в откройте **обозреватель сервера**.

2. Разверните пункт **Azure**, а затем — **HDInsight**.

3. При появлении запроса введите учетные данные подписки Azure и щелкните **Войти**.

4. Разверните кластер HDInsight, в который нужно развернуть это приложение. Отобразится запись с текстом __(Учетная запись хранения по умолчанию)__.

    ![В обозревателе серверов отображается учетная запись хранения для кластера](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Если эту запись можно развернуты, то для кластера в качестве хранилища по умолчанию используется __учетная запись хранения Azure__. Чтобы просмотреть файлы в хранилище по умолчанию кластера, разверните эту запись, а затем дважды щелкните запись __(Контейнер по умолчанию)__.

    * Если эту запись невозможно развернуты, то для кластера в качестве хранилища по умолчанию используется __Azure Data Lake Store__. Чтобы просмотреть файлы в хранилище по умолчанию кластера, дважды щелкните запись __(Контейнер по умолчанию)__.

6. Чтобы передать EXE-файлы, используйте один из следующих методов.

    * Если используется __учетная запись хранения Azure__, щелкните значок передачи и перейдите в папку **bin\debug** проекта **HiveCSharp**. Наконец, выберите файл **HiveCSharp.exe** и нажмите кнопку **OK**.

        ![значок отправки](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Если используется __Azure Data Lake Store__, щелкните правой кнопкой мыши пустое место в списке файлов и выберите __Отправить__. Выберите файл **HiveCSharp.exe** и нажмите кнопку **Открыть**.

    После завершения передачи __HiveCSharp.exe__ повторите этот процесс для передачи файла __PigUDF.exe__.

## <a name="run-a-hive-query"></a>Выполнение запроса Hive

1. В Visual Studio в откройте **обозреватель сервера**.

2. Разверните пункт **Azure**, а затем — **HDInsight**.

3. Щелкните правой кнопкой мыши кластер, в котором развернуто приложение **HiveCSharp**, а затем выберите **Написать запрос Hive**.

4. В качестве запроса Hive используйте следующий текст:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Раскомментируйте инструкцию `add file`, которая соответствует типу хранилища по умолчанию для кластера.

    Этот запрос предусматривает выбор полей `clientid`, `devicemake` и `devicemodel` в `hivesampletable` и их передачу в приложение HiveCSharp.exe. В результате запроса приложение должно возвратить три поля, которые хранятся под именами `clientid`, `phoneLabel` и `phoneHash`. Кроме этого, в корневом контейнере хранилища по умолчанию должен быть файл HiveCSharp.exe.

5. Щелкните **Отправить** , чтобы отправить задания в кластер HDInsight. Откроется окно **Сводка по заданию Hive**.

6. Щелкайте **Обновить** до тех пор, пока значение параметра **Состояние задания** не изменится на **Завершено**. Чтобы просмотреть выходные данные задания, щелкните **Выходные данные задания**.

## <a name="run-a-pig-job"></a>Выполнение задания Pig

1. Для подключения к кластеру HDInsight воспользуйтесь одним из следующих методов.

    * Если используется кластер HDInsight под управлением __Linux__, примените протокол SSH. Например, `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).
    
    * Для подключения к кластерам HDInsight __под управлением Windows__ используйте [удаленный рабочий стол](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. Для запуска командной строки Pig используйте одну из приведенных ниже команд.

        pig

    > [!IMPORTANT]
    > Если используется кластер под управлением Windows, используйте следующие команды.
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Откроется командная строка `grunt>`.

3. Чтобы запустить задание Pig, использующее приложение .NET Framework, введите следующую команду.

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    Инструкция `DEFINE` создает псевдоним `streamer` для приложения pigudf.exe, а `CACHE` загружает его из хранилища по умолчанию для кластера. Позже `streamer` используется с оператором `STREAM` для обработки отдельных строк ЖУРНАЛА и возвращает данные в виде ряда столбцов.

    > [!NOTE]
    > Имя приложения, которое используется для потоковой передачи, должно быть заключено в кавычки в виде обратного апострофа \` при использовании псевдонима и в одинарные кавычки (') при использовании с `SHIP`.

4. После ввода последней строки запустится задание. Появится результат, аналогичный приведенному ниже тексту.

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Дополнительная информация

В этом документе мы рассмотрели использование приложения .NET Framework из Hive и Pig в HDInsight. Чтобы узнать об использовании Python с Hive и Pig, см. статью [Использование Python с Hive и Pig в HDInsight](python-udf-hdinsight.md).

Сведения о других способах использования Pig и Hive и дополнительную информацию об использовании MapReduce см. в следующих документах:

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)
