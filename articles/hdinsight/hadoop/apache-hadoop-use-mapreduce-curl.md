---
title: "Использование MapReduce и Curl с Hadoop в HDInsight — Azure | Документы Майкрософт"
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
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: dd3e5904ee21ee74da5adaa65abd7865a82c8b36
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Выполнение заданий MapReduce с помощью REST в Hadoop в HDInsight

Узнайте, как с помощью REST API WebHCat выполнять задания MapReduce в Hadoop в кластере HDInsight. Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения заданий MapReduce, их мониторинга и получения их результатов.

> [!NOTE]
> Если вы уже знакомы с использованием серверов Hadoop на платформе Linux, но не знакомы с HDInsight, ознакомьтесь с документом [Сведения об использовании HDInsight в Linux](../hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Предварительные требования

* Hadoop в кластере HDInsight.
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Выполнение заданий MapReduce с помощью Curl

> [!NOTE]
> При использовании Curl или любых других средств связи REST с WebHCat нужно проводить аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
>
> В командах в этом разделе замените **admin** именем пользователя, используемым для аутентификации в кластере. Замените **CLUSTERNAME** именем кластера. При появлении запроса укажите пароль для учетной записи пользователя.
>
> API-интерфейс REST защищается с помощью [обычной проверки подлинности доступа](http://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.


1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Вы должны получить ответ, аналогичный показанному ниже коду JSON.

        {"status":"ok","version":"v1"}

    Ниже приведены параметры, используемые в этой команде:

   * **-u**: имя пользователя и пароль, используемые для аутентификации запроса.
   * **-G**: указывает, что это запрос GET.

   Начало URI **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** одинаковое для всех запросов.

2. Чтобы отправить задание MapReduce, используйте следующую команду:

    ```bash
    curl -u admin -d user.name=admin -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    Конец универсального кода ресурса (/mapreduce/jar) сообщает WebHCat, что этот запрос запускает задание MapReduce из класса в JAR-файле. Ниже приведены параметры, используемые в этой команде:

   * **-d**: `-G` не используется, поэтому в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемые в запросе.
    * **user.name**: пользователь, выполняющий команду.
    * **jar**: расположение JAR-файла, содержащего класс для запуска.
    * **class**: класс, содержащий логику MapReduce.
    * **arg**: аргументы, передаваемые в задание MapReduce. В данном случае это входной текстовый файл и каталог, который используется для вывода.

   Эта команда должна возвращать идентификатор задания, который может использоваться для проверки состояния задания:

       {"id":"job_1415651640909_0026"}

3. Чтобы проверить состояние задания, используйте следующую команду.

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Замените **JOBID** значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то JOBID будет `job_1415651640909_0026`.

    Если задание завершено, то возвращается состояние `SUCCEEDED`.

   > [!NOTE]
   > Этот запрос Curl возвращает документ JSON с информацией о задании. При этом jq используется только для получения значения состояния.

4. После изменения состояния задания на `SUCCEEDED` результаты задания можно получить из хранилища BLOB-объектов Azure. Параметр `statusdir`, передаваемый в запросе, содержит расположение выходного файла. В данном случае это `/example/curl`. Этот адрес задает каталог `/example/curl` для сохранения выходных данных задания, который размещен в хранилище по умолчанию для кластера.

Вы можете вывести список этих файлов и скачать их с помощью [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Дополнительные сведения о работе с большими двоичными объектами с помощью Azure CLI см. в документе [Использование Azure CLI 2.0 со службой хранилища Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительные сведения об интерфейсе REST, используемом в этой статье, см. в [справочнике по WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).