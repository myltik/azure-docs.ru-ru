<properties
   pageTitle="Использование Hadoop Pig в HDInsight | Azure"
   description="Информация об удаленной отправке заданий Pig в HDInsight с помощью Curl."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Выполнение запросов Hive с Hadoop в HDInsight с помощью Curl

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

В этом документе рассказывается о том, как с помощью Curl выполнять запросы Hive в Hadoop на кластере HDInsight. 

Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения запросов Hive, их мониторинга и получения их результатов. Для этого используется API REST для WebHCat (прежнее название - Templeton), предоставляемый кластером HDInsight.

> [AZURE.NOTE] Если вы уже знакомы с использованием серверов под управлением Linux Hadoop, но не знакомы с HDInsight, см. раздел <a href="../hdinsight-hadoop-linux-information/" target="_blank">Что необходимо знать о Hadoop в HDInsight на основе Linux</a>.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Hadoop в кластере HDInsight (на платформе Linux или Windows).

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

##<a id="curl"></a>Выполнение запросов Hive с помощью Curl

> [AZURE.NOTE] При использовании Curl или любых других средств связи REST с WebHCat нужно проводить аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
> 
> В командах, описанных в этом разделе, **USERNAME** нужно заменить именем пользователя для аутентификации в кластере, а **PASSWORD** - паролем учетной записи пользователя. Параметр **CLUSTERNAME** нужно заменить именем кластера.
> 
> API-Интерфейс REST защищается с помощью  <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">обычной проверки подлинности</a>. Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Вы должны получить ответ, аналогичный показанному ниже.

        {"status":"ok","version":"v1"}

    Ниже приведены параметры, используемые в этой команде.

    * **-u** - имя пользователя и пароль, используемый для аутентификации запроса.
    * **-G** - указывает, что это запрос GET.

    Начало URL-адреса (**https://CLUSTERNAME.azurehdinsight.net/templeton/v1**) будет одинаковым для всех запросов. Путь **/Status** указывает, что по запросу серверу должно быть возвращено состояние WebHCat (другое название - Templeton). Используя следующую команду, можно также запросить версию Hive.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Эта команда должна вернуть ответ следующего вида.

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Используйте следующую команду, чтобы создать новую таблицу с именем **log4jLogs**.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Ниже приведены параметры, используемые в этой команде.

    * **-d** - так как `-G` не используется, в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемых в запросе.

        * **user.name** - пользователь, выполняющий команду
        
        * **execute** - оператор HiveQL, который необходимо выполнить
        
        * **statusdir** - каталог, в который будет записано состояние этого задания

    Данная операция выполняет следующие действия:

    * **DROP TABLE** - приводит к удалению таблицы и файла данных в том случае, если таблица уже существует
    
    * **CREATE EXTERNAL TABLE** - создает новую внешнюю таблицу в Hive. Внешние таблицы хранят только описание самой таблицы в Hive, в то время как данные остаются в исходном расположении.

		> [AZURE.NOTE] Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
		>
		> Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только описание таблицы.

    * **ROW FORMAT** - указывает Hive, как следует форматировать данные. В данном случае поля всех журналов разделены пробелом.
    
    * **STORED AS TEXTFILE LOCATION** - указывает Hive место, где хранятся данные (например, в каталоге example/data), а также то, что они хранятся в виде текста
    
    * **SELECT** - подсчитывает количество строк, у которых столбец **t4** содержит значение **[ERROR]**. Эта команда должна вернуть значение **3**, так как таблица содержит три строки с данным значением.

    > [AZURE.NOTE] Обратите внимание, что при использовании Curl пробелы между операторами HiveQL заменяются знаком `+`. Заключенные в кавычки значения, содержащие пробелы в качестве разделителя, заменять на `+` не нужно.

    Эта команда должна возвращать идентификатор задания, который может использоваться для проверки состояния задания.

        {"id":"job_1415651640909_0026"}

3. Чтобы проверить состояние задания, используйте следующую команду. Замените **JOBID** значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то JOBID будет `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Если задание завершено, у него будет состояние "SUCCEEDED".

    > [AZURE.NOTE] Этот запрос Curl возвращает документ JSON с информацией о задании. При этом jq используется только для получения значения состояния. 

4. После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища больших двоичных объектов Azure. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это **wasb:///example/curl**. При использовании этого адреса выходные данные задания сохраняются в каталоге **example/curl** в контейнере хранилища, используемом по умолчанию кластером HDInsight.

    Можно перечислить и загрузить эти файлы с помощью <a href="../xplat-cli/" target="_blank">межплатформенного интерфейса командной строки (xplat-cli)</a>. Например, для просмотра списка файлов в **example/curl** можно использовать следующую команду.

		azure storage blob list <container-name> example/curl

	Чтобы скачать файл, используйте следующий код.

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] Необходимо либо указать имя учетной записи хранилища, которая содержит BLOB-объекты, с помощью параметров `-a` и `-k` или задать переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS\_KEY**. См. раздел <a href="../hdinsight-upload-data/" target="_blank" for more information.

6. Используйте следующие операторы, чтобы создать новую таблицу internal с именем **errorLogs**.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Данная операция выполняет следующие действия:

    * **CREATE TABLE IF NOT EXISTS** - будет создана таблица, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не было использовано, данная таблица будет являться "внутренней", то есть храниться в хранилище данных Hive и полностью обслуживаться Hive.

		> [AZURE.NOTE] В отличие от **ВНЕШНИХ** таблиц, удаление внутренних таблиц приводит также к удалению данных.

    * **STORED AS ORC** - данные будут храниться в формате Optimized Row Columnar (ORC). Это высокооптимизированный и эффективный формат для хранения данных Hive.
    * **INSERT OVERWRITE ... SELECT** - выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]**, после чего данные будут вставлены в таблицу **errorLogs**
    * **SELECT * ** - выбирает все строки из новой таблицы **errorLogs**.

7. Используйте идентификатор задания, возвращаемый для проверки состояния задания. После его успешного выполнения, используйте xplat-cli, как было описано ранее, чтобы скачать и просмотреть результаты. Выходные данные должны содержать три строки, в каждой из которых должен быть текст **[ERROR]**.


##<a id="summary"></a>Сводка

Как показано в этом документе, для запуска, мониторинга и просмотра результатов выполнения заданий Hive в кластере HDInsight можно использовать необработанные HTTP-запросы.

Дополнительную информацию об интерфейсе REST, используемом в этой статье, см. в статье <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">Ссылки WebHCat</a>.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация об использовании Hive в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->
