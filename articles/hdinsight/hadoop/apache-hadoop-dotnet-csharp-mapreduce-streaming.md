---
title: Использование C# с MapReduce в Hadoop HDInsight в Azure | Документация Майкрософт
description: Узнайте, как использовать C# для создания решений MapReduce, использующих Hadoop в Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 7287972ccf63f33a8cf08065f8d5d30ee1b1afb5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400883"
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Использование языка C# для потоковой передачи MapReduce в Hadoop в HDInsight

Узнайте, как использовать C# для создания решения MapReduce в HDInsight.

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)

Потоковая передача Hadoop обеспечивается служебной программой, которая позволяет запускать задания MapReduce с помощью сценария или исполняемого файла. В этом примере .NET используется для реализации модулей сопоставления и редукции для решения для подсчета слов.

## <a name="net-on-hdinsight"></a>.NET в HDInsight

В кластерах __HDInsight под управлением Linux__ для запуска приложений .NET используется [Mono (https://mono-project.com)](https://mono-project.com). Mono версии 4.2.1 входит в состав HDInsight версии 3.6. Дополнительные сведения о версии Mono, которая входит в состав HDInsight, см. в разделе [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md) Чтобы использовать определенную версию Mono, см. статью об [установке или обновлении Mono](../hdinsight-hadoop-install-mono.md).

Дополнительные сведения о совместимости Mono с различными версиями платформы .NET Framework см. в разделе [Compatibility](http://www.mono-project.com/docs/about-mono/compatibility/) (Совместимость).

## <a name="how-hadoop-streaming-works"></a>Как работает потоковая передача Hadoop

Базовый процесс потоковой передачи в данном документе выглядит следующим образом.

1. Hadoop передает данные в модуль сопоставления (mapper.exe в этом примере) через канал STDIN.
2. Модуль сопоставления обрабатывает эти данные и передает пары "ключ-значение", разделенные знаками табуляции, в канал STDOUT.
3. Выходные данные считываются Hadoop и затем передаются в модуль редукции (reducer.exe в этом примере) на STDIN.
4. Модуль редукции считывает пары "ключ-значение", разделенные знаками табуляции, обрабатывает данные и передает результат в виде пар "ключ-значение", разделенные знаками табуляции, в канал STDOUT.
5. Выходные данные считываются Hadoop и записываются в выходной каталог.

Дополнительные сведения см. в документации по [потоковой передаче Hadoop (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>предварительным требованиям

* Опыт написания и выполнения сборки кода C#, предназначенного для платформы .NET Framework 4.5. В этом руководстве используется Visual Studio 2017.

* Способ передачи EXE-файлов в кластер. В этом документе для передачи файлов в основное хранилище кластера используются средства Data Lake для Visual Studio.

* Azure PowerShell или SSH-клиент.

* Hadoop в кластере HDInsight. Дополнительные сведения о создании кластера см. в статье [Создание кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Создание модуля сопоставления

Создайте __консольное приложение__ в Visual Studio и назовите его __mapper__. Используйте для него следующий код.

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

После создания приложения выполните его сборку, чтобы создать файл `/bin/Debug/mapper.exe` в каталоге проекта.

## <a name="create-the-reducer"></a>Создание модуля редукции

Создайте __консольное приложение__ в Visual Studio и назовите его __reducer__. Используйте для него следующий код.

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

После создания приложения выполните его сборку, чтобы создать файл `/bin/Debug/reducer.exe` в каталоге проекта.

## <a name="upload-to-storage"></a>Отправка в хранилище

1. В Visual Studio в откройте **обозреватель сервера**.

2. Разверните пункт **Azure**, а затем — **HDInsight**.

3. При появлении запроса введите учетные данные подписки Azure и щелкните **Войти**.

4. Разверните кластер HDInsight, в который нужно развернуть это приложение. Отобразится запись с текстом __(Учетная запись хранения по умолчанию)__.

    ![В обозревателе серверов отображается учетная запись хранения для кластера](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Если эту запись можно развернуты, то для кластера в качестве хранилища по умолчанию используется __учетная запись хранения Azure__. Чтобы просмотреть файлы в хранилище по умолчанию кластера, разверните эту запись, а затем дважды щелкните запись __(Контейнер по умолчанию)__.

    * Если эту запись невозможно развернуты, то для кластера в качестве хранилища по умолчанию используется __Azure Data Lake Store__. Чтобы просмотреть файлы в хранилище по умолчанию кластера, дважды щелкните запись __(Контейнер по умолчанию)__.

5. Чтобы передать EXE-файлы, используйте один из следующих методов.

    * Если используется __учетная запись хранения Azure__, щелкните значок передачи и перейдите в папку **bin\debug** проекта **mapper**. Выберите файл **mapper.exe** и нажмите кнопку **OK**.

        ![значок отправки](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * Если используется __Azure Data Lake Store__, щелкните правой кнопкой мыши пустое место в списке файлов и выберите __Отправить__. Выберите файл **mapper.exe** и нажмите кнопку **Открыть**.

    После завершения передачи __mapper.exe__ повторите этот процесс для передачи файла __reducer.exe__.

## <a name="run-a-job-using-an-ssh-session"></a>Выполнение задания с помощью сеанса SSH

1. Подключитесь к кластеру HDInsight с помощью SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Используйте одну из приведенных команд для запуска задания MapReduce.

    * Если в качестве хранилища по умолчанию используется __Azure Data Lake Store__:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Если в качестве хранилища по умолчанию используется __служба хранилища Azure__:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    Ниже перечислены функции каждого из параметров.

    * `hadoop-streaming.jar`: JAR-файл, содержащий функции потоковой передачи MapReduce.
    * `-files`: добавляет файлы `mapper.exe` и `reducer.exe` в это задание. `adl:///` или `wasb:///` перед именем файла — это путь к корню хранилища по умолчанию для кластера.
    * `-mapper`: задает файл, который реализует модуль сопоставления.
    * `-reducer`: задает файл, который реализует модуль редукции.
    * `-input`: входные данные.
    * `-output`: выходной каталог.

3. После завершения задания MapReduce просмотрите результаты с помощью следующей команды.

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Ниже приведен пример данных, возвращаемых этой командой.

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Выполнение задания с помощью PowerShell

Используйте приведенный ниже сценарий PowerShell для запуска задания MapReduce и скачивания результатов.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Этот сценарий предлагает ввести имя учетной записи для входа и пароль для кластера, а также имя кластера HDInsight. После завершения задания выходные данные скачиваются в файл `output.txt`. Ниже приведен пример выходных данных в файле `output.txt`.

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об использовании MapReduce с HDInsight см. в разделе [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md).

Сведения об использовании языка C# с Hive и Pig см. в разделе [Использование определяемых пользователем функций C# при потоковой передаче Hive и Pig в Hadoop HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Сведения об использовании языка C# со Storm в HDInsight см. в разделе [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
