---
title: Использование Hadoop Sqoop с Curl в HDInsight — Azure | Документы Майкрософт
description: Узнайте об удаленной отправке заданий Sqoop в HDInsight с помощью Curl.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: a83b87f1ed052c6d21d337eb37bc560efbf118ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202151"
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Выполнение заданий Sqoop с Hadoop в HDInsight с помощью Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как с помощью Curl выполнять задания Sqoop в кластере Hadoop в HDInsight.

Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения и мониторинга заданий Sqoop, а также получения их результатов. Для этого используется REST API для WebHCat (прежнее название — Templeton), предоставляемый кластером HDInsight.

## <a name="prerequisites"></a>предварительным требованиям
Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Выполните инструкции, приведенные в статье [Использование Sqoop с Hadoop в HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database), чтобы настроить среду с помощью кластера HDInsight и базы данных SQL Azure.
* [Curl](http://curl.haxx.se/). Curl — это средство для передачи данных в кластер HDInsight или из него.
* [jq](http://stedolan.github.io/jq/). Служебная программа jq используется для обработки данных JSON, возвращаемых запросами REST.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Отправка заданий Sqoop с помощью Curl
> [!NOTE]
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
> 
> В командах, описанных в этом разделе, замените **USERNAME** на имя пользователя для выполнения проверки подлинности в кластере, а **PASSWORD** — на пароль учетной записи пользователя. Замените **CLUSTERNAME** именем кластера.
> 
> REST API защищен с помощью [обычной проверки подлинности](http://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.
> 
> 

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Вы должны получить ответ, аналогичный показанному ниже:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    Ниже приведены параметры, используемые в этой команде:
   
   * **-u** — имя пользователя и пароль, используемый для аутентификации запроса.
   * **-G** — указывает, что это запрос GET.
     
     Начало URL-адреса **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** одинаковое для всех запросов. Путь **/status** указывает, что по запросу серверу должно быть возвращено состояние WebHCat (другое название — Templeton). 
2. Чтобы отправить задание Sqoop, воспользуйтесь следующей командой:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Ниже приведены параметры, используемые в этой команде:

    * **-d** — так как `-G` не используется, в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемые в запросе.

        * **user.name** — пользователь, выполняющий команду.

        * **command** — выполняемая команда Sqoop.

        * **statusdir** — каталог, в который будет записано состояние этого задания.

    Эта команда возвратит идентификатор задания, который может использоваться для проверки состояния задания.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Чтобы проверить состояние задания, используйте следующую команду. Замените **JOBID** значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то **JOBID** будет `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Если задание завершено, у него будет состояние **SUCCEEDED**(Успешно).
   
   > [!NOTE]
   > Этот запрос Curl возвращает документ нотации объектов JavaScript с информацией о задании. При этом jq используется только для получения значения состояния.
   > 
   > 
4. После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища больших двоичных объектов Azure. Параметр `statusdir`, передаваемый с запросом, содержит расположение выходного файла. В нашем примере это **wasb:///example/data/sqoop/curl**. При использовании этого адреса выходные данные задания сохраняются в каталоге **example/data/sqoop/curl** в контейнере хранилища, используемом по умолчанию кластером HDInsight.
   
    Портал Azure можно использовать для доступа к большим двоичным объектам stderr и stdout.  Также можно использовать Microsoft SQL Server Management Studio для проверки данных, передаваемых в таблицу log4jlogs.

## <a name="limitations"></a>Ограничения
* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, пока не поддерживает операции массовой вставки.
* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется переключатель `-batch` , Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="summary"></a>Сводка
Как показано в этом документе, для запуска, мониторинга и просмотра результатов выполнения заданий Sqoop в кластере HDInsight можно использовать необработанные HTTP-запросы.

Дополнительную информацию об интерфейсе REST, используемом в этой статье, см. в <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">справочнике по Sqoop REST API</a>.

## <a name="next-steps"></a>Дополнительная информация
Общая информация об использовании Hive в HDInsight:

* [Использование Sqoop с Hadoop в HDInsight (Windows)](hdinsight-use-sqoop.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Другие статьи об HDInsight, в которых используется curl:
 
* [Создание кластеров Hadoop с помощью Azure REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Выполнение запросов Hive с Hadoop в HDInsight с помощью REST](apache-hadoop-use-hive-curl.md)
* [Выполнение заданий MapReduce с помощью REST в Hadoop в HDInsight](apache-hadoop-use-mapreduce-curl.md)
* [Выполнение запросов Pig с помощью cURL с использованием Hadoop в HDInsight](apache-hadoop-use-pig-curl.md)



