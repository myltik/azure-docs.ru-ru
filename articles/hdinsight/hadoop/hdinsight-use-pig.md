---
title: Использование Hadoop Pig в HDInsight | Документация Майкрософт
description: Научитесь использовать Pig с Hadoop в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: d6d42a1e0edb038f6d4cc9c8149768bdb698cafc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Использование Pig с Hadoop в HDInsight

Узнайте, как использовать [Apache Pig](http://pig.apache.org/) с HDInsight.

Pig — это платформа, позволяющая создавать программы для Hadoop с помощью процедурного языка, известного как *Pig Latin*. Pig, который является альтернативой Java для создания решений *MapReduce* , входит в состав Azure HDInsight. Воспользуйтесь приведенной таблицей, чтобы узнать, как можно использовать Pig с HDInsight.

| **Используйте** , если требуется... | ... **интерактивная** оболочка | ...**пакетная** обработка | ...with этим **кластером операционной системы** | ...из этого **кластера операционной системы** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X или Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux или Windows |Linux, Unix, Mac OS X или Windows |
| [.NET SDK для Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Windows или Linux |Windows (сейчас) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Windows или Linux |Windows |

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Почему именно Pig?

Одна из трудностей обработки данных с помощью MapReduce в Hadoop заключается в реализации логики обработки с использованием только функций map и reduce. Для сложных задач часто приходится разбивать обработку на несколько операций MapReduce, соединенных друг с другом, чтобы достичь желаемого результата.

Pig позволяет определить обработку как ряд преобразований, через которые проходят данные для получения желаемого результата.

Язык Pig Latin позволяет описывать поток данных так, чтобы из необработанных входных данных можно было получить нужный результата посредством одного или нескольких преобразований. Программы Pig Latin следуют общему шаблону:

* **Загрузка**: чтение данных для обработки из файловой системы.

* **Преобразование**: обработка данных.

* **Дамп или сохранение**: вывод данных на экран или сохранение их для дальнейшей обработки.

### <a name="user-defined-functions"></a>Определяемые пользователем функции

Pig Latin также поддерживает пользовательские функции, позволяющие вызывать внешние компоненты, реализующие логику, которую трудно смоделировать в Pig Latin.

Дополнительные сведения о Pig Latin см. в [справочном руководстве по Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) и [справочном руководстве по Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Примеры использования пользовательских функций в Hive см. в следующих документах:

* [Использование DataFu с Pig в HDInsight](apache-hadoop-use-pig-datafu-udf.md) — DataFu представляет собой коллекцию полезных определяемых пользователем функций, поддерживаемых Apache
* [Использование Python с Pig и Hive в HDInsight](python-udf-hdinsight.md)
* [Использование C# с Hive и Pig в HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Демонстрационные данные

HDInsight предоставляет различные наборы демонстрационных данных, которые хранятся в каталогах `/example/data` и `/HdiSamples`. Эти каталоги находятся в хранилище по умолчанию для кластера. В примере Pig в этом документе используется файл *log4j* из `/example/data/sample.log`.

Каждый журнал в файле состоит из строки полей, содержащей поле `[LOG LEVEL]` для отображения типа и серьезности.

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

В предыдущем примере уровень журнала имеет значение ERROR ("ошибка").

> [!NOTE]
> Вы также можете создать файл log4j с помощью средства ведения журнала [Apache Log4j](http://en.wikipedia.org/wiki/Log4j), а затем отправить этот файл в большой двоичный объект. Дополнительные сведения см. в статье [Отправка данных для заданий Hadoop в HDInsight](../hdinsight-upload-data.md). Дополнительные сведения о том, каким образом большие двоичные объекты в службе хранилища Azure используются с HDInsight, см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Пример задания

Следующее задание Pig Latin загружает файл `sample.log` из хранилища по умолчанию для кластера HDInsight. Затем оно выполняет ряд преобразований, которые позволяют подсчитать, сколько раз каждый из уровней ведения журнала был указан во входных данных. Результаты записываются в поток STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

На следующем рисунке показано, что каждое из преобразований делает с данным.

![Графическое представление преобразований][image-hdi-pig-data-transformation]

## <a id="run"></a>Запуск задания Pig Latin

HDInsight может запускать задания Pig Latin с помощью различных методов. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке к пошаговому руководству.

| **Используйте** , если требуется... | ... **интерактивная** оболочка | ...**пакетная** обработка | ...with этим **кластером операционной системы** | … в этом **клиенте** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X или Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux или Windows |Linux, Unix, Mac OS X или Windows |
| [.NET SDK для Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Windows или Linux |Windows (сейчас) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Windows или Linux |Windows |

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>Использование Pig и SQL Server Integration Services

С помощью служб SQL Server Integration Services (SSIS) можно выполнить задание Pig. Пакет дополнительных компонентов Azure для служб SSIS предоставляет следующие компоненты, которые работают с заданиями Pig в HDInsight.

* [Задача Pig в Azure HDInsight][pigtask]

* [Диспетчер подключений по подпискам Azure][connectionmanager]

Узнать больше о пакете дополнительных компонентов Azure для служб SSIS можно [здесь][ssispack].

## <a id="nextsteps"></a>Дальнейшие действия
Теперь, когда вы узнали, как использовать Pig в HDInsight, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Отправка данных в HDInsight](../hdinsight-upload-data.md)
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Sqoop с HDInsight](hdinsight-use-sqoop.md)
* [Использование Oozie с HDInsight](../hdinsight-use-oozie.md)
* [Использование заданий MapReduce с HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
