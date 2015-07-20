<properties
   pageTitle="Использование Hadoop Pig в HDInsight | Microsoft Azure"
   description="Научитесь использовать Pig с Hadoop в HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

# Использование Pig с Hadoop в HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache Pig](http://pig.apache.org/) — это платформа, позволяющая создавать программы для Hadoop с помощью процедурного языка, известного как *Pig Latin*. Pig, который является альтернативой Java для создания решений *MapReduce*, входит в состав Azure HDInsight.

Из этой статьи вы узнаете, как использовать Pig с HDInsight.

##<a id="why"></a>Почему именно Pig?

Одна из трудностей обработки данных с помощью MapReduce в Hadoop заключается в реализации логики обработки с использованием только функций map и reduce. Для сложных задач часто приходится разбивать обработку на несколько операций MapReduce, соединенных друг с другом, чтобы достичь желаемого результата.

Вместо того чтобы принуждать вас к использованию только функций map и reduce, Pig позволяет определить обработку как ряд преобразований, через которые проходят данные для получения желаемого вывода.

Язык Pig Latin позволяет описывать поток данных так, чтобы из необработанных входных данных можно было получить нужный результата посредством одного или нескольких преобразований. Программы Pig Latin следуют общему шаблону:

- **Загрузка**: чтение данных для обработки из файловой системы.
- **Преобразование**: обработка данных.
- **Дамп или сохранение**: вывод данных на экран или сохранение их для дальнейшей обработки.

Pig Latin также поддерживает пользовательские функции, позволяющие вызывать внешние компоненты, реализующие логику, которую трудно смоделировать в Pig Latin.

Дополнительные сведения о Pig Latin см. в [справочном руководстве по Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) и [справочном руководстве по Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Примеры использования пользовательских функций в Hive см. в следующих документах:

* [Использование Python с Pig и Hive в HDInsight](hdinsight-python.md)

* [Использование C# с Hive и Pig в HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Сведения о демонстрационных данных

В этом примере используется пример файла *log4j*, который хранится в контейнере хранилища BLOB-объектов (путь к файлу **/example/data/sample.log**). Каждый журнал в файле состоит из строки полей, содержащей поле `[LOG LEVEL]` для отображения типа и серьезности.

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

В предыдущем примере уровень журнала имеет значение ERROR («ошибка»).

> [AZURE.NOTE]Вы также можете создать файл log4j с помощью средства ведения журнала [Apache Log4j](http://en.wikipedia.org/wiki/Log4j), а затем отправить этот файл в большой двоичный объект. См. раздел [Отправка данных в HDInsight](hdinsight-upload-data.md) для получения более подробной информации. Дополнительные сведения о том, каким образом большие двоичные объекты в службе хранилища Azure используются с HDInsight, см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).

Демонстрационные данные хранятся в хранилище BLOB-объектов Azure, используемом HDInsight в качестве файловой системы по умолчанию для кластеров Hadoop. HDInsight может обращаться к файлам хранилища BLOB-объектов с помощью префикса **wasb**. Поэтому для доступа к файлу sample.log необходимо использовать следующий синтаксис:

	wasb:///example/data/sample.log

Так как WASB является хранилищем по умолчанию для HDInsight, обратиться к файлу также можно из Pig Latin, используя путь **/example/data/sample.log**.

> [AZURE.NOTE]Синтаксис **wasb:///** используется для обращения к файлам, которые хранятся в контейнере хранилища по умолчанию для вашего кластера HDInsight. Если вы указали дополнительные учетные записи хранения при подготовке кластера и хотите получить доступ к файлам, хранящимся в них, это можно сделать, указав имя контейнера и адрес учетной записи хранения, например: **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Сведения о примере приложения

Следующее задание Pig Latin загружает файл **sample.log** из хранилища по умолчанию для кластера HDInsight. Затем оно выполняет ряд преобразований, которые позволяют подсчитать, сколько раз каждый из уровней ведения журнала был указан во входных данных. Дамп результатов записывается в STDOUT.

	LOGS = LOAD 'wasb:///example/data/sample.log';
	LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
	FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
	GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
	FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
	RESULT = order FREQUENCIES by COUNT desc;
	DUMP RESULT;

На следующем рисунке показано, что каждое из преобразований делает с данным.

![Графическое представление преобразований][image-hdi-pig-data-transformation]

##<a id="run"></a>Запуск задания Pig Latin

HDInsight может запускать задания Pig Latin с помощью различных методов. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке, чтобы открыть пошаговое руководство.

| **Используйте**, если требуется... | ...**интерактивная** оболочка | ...**пакетная** обработка | ... с **кластерной операционной системой** | ...из этого **кластера операционной системы** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X или Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) | &nbsp; | ✔ | Linux или Windows | Linux, Unix, Mac OS X или Windows |
| [.NET SDK для Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux или Windows | Windows (сейчас) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) | &nbsp; | ✔ | Linux или Windows | Windows |
| [Удаленный рабочий стол](hdinsight-hadoop-use-pig-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

##<a id="nextsteps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать Pig в HDInsight, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование заданий MapReduce с HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

<!---HONumber=July15_HO2-->