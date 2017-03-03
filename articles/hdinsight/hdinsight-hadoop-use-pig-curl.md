---
title: "Использование Hadoop Pig с Curl в HDInsight | Документация Майкрософт"
description: "Узнайте, как с помощью Curl выполнять задания Pig Latin в кластере Hadoop в Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ecc141c9afa46f23d31de4356068ef4f98a92aa
ms.openlocfilehash: d4d9ed8380a0e8726fe2e2835e4b10fd262e1562
ms.lasthandoff: 02/10/2017


---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Выполнение заданий Pig с помощью Curl с использованием Hadoop в HDInsight

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

В этом документе рассказывается о том, как с помощью Curl выполнять задания Pig Latin в кластере Azure HDInsight. Язык программирования Pig Latin позволяет описывать преобразования, применяемые к входным данным для получения требуемых выходных данных.

Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения заданий Pig, их мониторинга и получения их результатов. Для этого используется REST API для WebHCat (прежнее название — Templeton), предоставляемый кластером HDInsight.

> [!NOTE]
> Если вы уже знаете, как использовать серверы Hadoop на платформе Linux, но не знакомы с HDInsight, ознакомьтесь со статьей [Советы по использованию HDInsight на платформе Linux](hdinsight-hadoop-linux-information.md).

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Linux или Windows).

  > [!IMPORTANT]
  > Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a name="a-idcurlarun-pig-jobs-by-using-curl"></a><a id="curl"></a>Выполнение заданий Pig с помощью Curl

> [!NOTE]
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
> 
> В командах, описанных в этом разделе, замените **USERNAME** на имя пользователя для выполнения проверки подлинности в кластере, а **PASSWORD** — на пароль учетной записи пользователя. Замените **CLUSTERNAME** именем кластера.
> 
> REST API защищается с помощью [обычной проверки подлинности](http://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    Вы должны получить ответ, аналогичный показанному ниже:
   
        {"status":"ok","version":"v1"}
   
    Ниже приведены параметры, используемые в этой команде:
   
    * **-u**— имя пользователя и пароль, используемый для аутентификации запроса.
    * **-G**— указывает, что это запрос GET.
     
     Начало URL-адреса **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** будет одинаковым для всех запросов. Путь **/status** указывает, что по запросу серверу должно быть возвращено состояние WebHCat (другое название — Templeton).

2. Чтобы отправить задание Pig Latin в кластер, используйте следующий код:
   
        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
   
    Ниже приведены параметры, используемые в этой команде:
   
    * **-d** — так как `-G` не используется, в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемые в запросе.
    
    * **user.name**— пользователь, выполняющий команду.
    * **execute**— оператор Pig Latin, который необходимо выполнить.
    * **statusdir**— каталог, в который будет записано состояние этого задания.
    
    > [!NOTE]
    > Обратите внимание, что при использовании Curl пробелы в операторах Pig Latin заменяются знаком `+`.
    
    Эта команда должна возвращать идентификатор задания, который может использоваться для проверки состояния задания. Пример:
     
        {"id":"job_1415651640909_0026"}

3. Чтобы проверить состояние задания, используйте следующую команду. Замените **JOBID** значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то **JOBID** будет `job_1415651640909_0026`.
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    Если задание завершено, у него будет состояние **SUCCEEDED**(Успешно).
   
    > [!NOTE]
    > Этот запрос Curl возвращает документ JSON с информацией о задании. При этом jq используется только для получения значения состояния.

## <a name="a-idresultsaview-results"></a><a id="results"></a>Просмотр результатов

После изменения состояния задания на **Успешно** результаты задания можно получить из хранилища по умолчанию, которое использует кластер. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это **/example/pigcurl**. 

В качестве резервного хранилища для HDInsight можно использовать службу хранилища Azure или Azure Data Lake Store. Данные можно получить разными способами в зависимости от используемого хранилища. Дополнительные сведения о работе со службой хранилища Azure и Azure Data Lake Store см. в разделе [HDFS, хранилище BLOB-объектов и Azure Data Lake Store](hdinsight-hadoop-linux-information.md##hdfs-blob-storage-and-data-lake-store) в документе об использовании HDInsight в Linux.

## <a name="a-idsummaryasummary"></a><a id="summary"></a>Сводка

Как показано в этом документе, для запуска, мониторинга и просмотра результатов выполнения заданий Pig в кластере HDInsight можно использовать необработанные HTTP-запросы.

Дополнительные сведения об интерфейсе REST, используемом в этой статье, см. в [справочнике по WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)


