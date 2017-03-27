---
title: "Использование MapReduce и Curl с Hadoop в HDInsight | Документация Майкрософт"
description: "Информация об удаленном выполнении заданий MapReduce с помощью Curl с использованием Hadoop в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 41733f76e5f0c82c5321158d6a919f35f6faee3a
ms.lasthandoff: 03/21/2017


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Выполнение заданий MapReduce с помощью Curl с использованием Hadoop в HDInsight
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

В этом документе рассказывается о том, как с помощью Curl выполнять задания MapReduce в Hadoop на кластере HDInsight.

Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения заданий MapReduce, их мониторинга и получения их результатов. Для этого используется REST API для WebHCat (прежнее название — Templeton), предоставляемый кластером HDInsight.

> [!NOTE]
> Если вы уже знакомы с использованием серверов Hadoop на платформе Linux, но не знакомы с HDInsight, см. статью [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Предварительные требования
Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Hadoop в кластере HDInsight.
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Выполнение заданий MapReduce с помощью Curl
> [!NOTE]
> При использовании Curl или любых других средств связи REST с WebHCat нужно проводить аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
> 
> В командах, описанных в этом разделе, **USERNAME** нужно заменить именем пользователя для аутентификации в кластере, а **PASSWORD** — паролем учетной записи пользователя. Замените **CLUSTERNAME** именем кластера.
> 
> API-интерфейс REST защищается с помощью [обычной проверки подлинности доступа](http://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.
> 
> 

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    Вы должны получить ответ, аналогичный показанному ниже:
   
        {"status":"ok","version":"v1"}
   
    Ниже приведены параметры, используемые в этой команде:
   
   * **-u**: имя пользователя и пароль, используемые для аутентификации запроса.
   * **-G**— указывает, что это запрос GET.
     
     Начало URI **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** одинаковое для всех запросов.
2. Чтобы отправить задание MapReduce, используйте следующую команду:
   
        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
   
    Конец универсального кода ресурса (/mapreduce/jar) сообщает WebHCat, что этот запрос запускает задание MapReduce из класса в JAR-файле. Ниже приведены параметры, используемые в этой команде:
   
   * **-d**: `-G` не используется, поэтому в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемые в запросе.
     
     * **user.name**: пользователь, выполняющий команду.
     * **jar**: расположение JAR-файла, содержащего класс для запуска.
     * **class**: класс, содержащий логику MapReduce.
     * **arg**: аргументы, передаваемые в задание MapReduce. В данном случае это входной текстовый файл и каталог, которые используются для вывода.
     
     Эта команда должна возвращать идентификатор задания, который может использоваться для проверки состояния задания:
     
       {"id":"job_1415651640909_0026"}
3. Чтобы проверить состояние задания, используйте следующую команду. Замените **JOBID** значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то JOBID будет `job_1415651640909_0026`.
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    Если задание завершено, у него будет состояние «SUCCEEDED».
   
   > [!NOTE]
   > Этот запрос Curl возвращает документ JSON с информацией о задании. При этом jq используется только для получения значения состояния.
   > 
   > 
4. После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища BLOB-объектов Azure. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это **wasbs:///example/curl**. При использовании этого адреса выходные данные задания сохраняются в каталоге **example/curl** в стандартном контейнере хранилища для кластера HDInsight.

Вы можете вывести список этих файлов и скачать их с помощью [интерфейса командной строки Azure](../cli-install-nodejs.md). Например, для просмотра списка файлов в **example/curl**можно использовать следующую команду:

    azure storage blob list <container-name> example/curl

Чтобы скачать файл:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [!NOTE]
> Необходимо указать имя учетной записи хранения, содержащей большой двоичный объект, с помощью параметров `-a` и `-k` или задать переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS\_KEY**. Дополнительные сведения см. в статье [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md).
> 
> 

## <a id="summary"></a>Сводка
Как показано в этом документе, для запуска, мониторинга и просмотра результатов выполнения заданий Hive в кластере HDInsight можно использовать необработанные HTTP-запросы.

Дополнительные сведения об интерфейсе REST, используемом в этой статье, см. в [справочнике по WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Дальнейшие действия
Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)


