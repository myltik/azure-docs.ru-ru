<properties
   pageTitle="Использование Hadoop Pig в HDInsight для платформы Azure"
   description="Информация об использовании Pig с Hadoop в HDInsight с применением протокола SSH."
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
 
#Выполнение заданий Pig с помощью Curl с использованием Hadoop в HDInsight

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

В этом документе рассказывается о том, как с помощью Curl выполнять задания Pig Latin в Hadoop на кластере HDInsight. Язык программирования Pig Latin позволяет описывать преобразования, применяемые к входным данным для получения требуемых выходных данных.

Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения заданий Pig, их мониторинга и получения их результатов. Для этого используется API REST для WebHCat (прежнее название - Templeton), предоставляемый кластером HDInsight.

> [AZURE.NOTE] Если вы уже знаете, как использовать серверы Hadoop на платформе Linux, но не знакомы с HDInsight, см. раздел <a href="../hdinsight-hadoop-linux-information/" target="_blank">"Что нужно знать о Hadoop в HDInsight на платформе Linux"</a>.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux).

* <a href="http://curl.haxx.se/" target="_blank">Curl.</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq.</a>

##<a id="curl"></a>Выполнение заданий Pig с помощью Curl

> [AZURE.NOTE] При использовании Curl или любых других средств связи REST с WebHCat нужно проводить аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
> 
> В командах, описанных в этом разделе, **USERNAME** нужно заменить именем пользователя для аутентификации в кластере, а **PASSWORD** - паролем учетной записи пользователя. Параметр **CLUSTERNAME** нужно заменить именем кластера.
> 
> Для защиты REST API используется <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">обычная проверка подлинности</a>. Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight. 

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Вы должны получить ответ, аналогичный показанному ниже.

        {"status":"ok","version":"v1"}

    Ниже приведены параметры, используемые в этой команде.

    * **-u** - имя пользователя и пароль, используемый для аутентификации запроса.
    * **-G** - указывает, что это запрос GET.

    Начало URL-адреса (**https://CLUSTERNAME.azurehdinsight.net/templeton/v1**) будет одинаковым для всех запросов. Путь **/Status** указывает, что по запросу серверу должно быть возвращено состояние WebHCat (другое название - Templeton).

2. Чтобы отправить задание Pig Latin в кластер, используйте следующую команду.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Ниже приведены параметры, используемые в этой команде.

    * **-d** - так как `-G` не используется, в запросе по умолчанию используется метод POST. `-d` - задает значения данных, отправляемых в запросе.

        * **user.name** - пользователь, выполняющий команду.
        * **execute** - оператор Pig Latin, который необходимо выполнить.
        * **statusdir** - каталог, в который будет записано состояние этого задания.

    > [AZURE.NOTE] Обратите внимание, что при использовании Curl пробелы в операторах Pig Latin заменяются знаком `+`.

    Эта команда должна возвращать идентификатор задания, который может использоваться для проверки состояния задания.

        {"id":"job_1415651640909_0026"}

3. Чтобы проверить состояние задания, используйте следующую команду. Замените **JOBID** значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то JOBID будет `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Если задание завершено, у него будет состояние "SUCCEEDED".

    > [AZURE.NOTE] Этот запрос Curl возвращает документ JSON с информацией о задании. При этом jq используется только для получения значения состояния. 

##<a id="results"></a>Просмотр результатов

После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища больших двоичных объектов Azure. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это **wasb:///example/pigcurl**. При использовании этого адреса выходные данные задания сохраняются в каталоге **example/pigcurl** в контейнере хранилища, используемом по умолчанию кластером HDInsight.

Вы можете вывести список этих файлов и скачать их с помощью <a href="../xplat-cli/" target="_blank">кроссплатформенного интерфейса командной строки Azure (xplat-cli)</a>. Например, для просмотра списка файлов в **example/pigcurl** можно использовать следующую команду.

	azure storage blob list <container-name> example/pigcurl

Чтобы скачать файл, используйте следующий код.

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Необходимо либо указать имя учетной записи хранения, содержащей большой двоичный объект, с помощью параметров `-a` и `-k`, либо задать переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS\_KEY**. См. <a href="../hdinsight-upload-data/" target="_blank" for more information..


##<a id="summary"></a>Сводка

Как показано в этом документе, для запуска, мониторинга и просмотра результатов выполнения заданий Pig в кластере HDInsight можно использовать необработанные HTTP-запросы.

Дополнительную информацию об интерфейсе REST, используемом в этой статье, см. в статье [WebHCat Reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight.

* [Использование Pig с Hadoop в HDInsight](../hdinsight-use-pig/)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](../hdinsight-use-hive/)

* [Использование MapReduce с Hadoop в HDInsight](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
