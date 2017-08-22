---
title: "Устранение неполадок с YARN в Azure HDInsight | Документация Майкрософт"
description: "Ознакомьтесь с ответами на часто задаваемые вопросы о Yarn на платформе Azure HDInsight."
keywords: "Azure HDInsight, Yarn, вопросы и ответы, руководство по устранению неполадок, часто задаваемые вопросы"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017


---

# <a name="yarn-troubleshooting"></a>Устранение неполадок с YARN

В этой статье описываются основные проблемы и способы их устранения при работе с полезными данными YARN в Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Как создать новую очередь Yarn в кластере?

### <a name="issue"></a>Проблема

Создание новой очереди Yarn с выделением емкости в кластере HDInsight.  

### <a name="resolution-steps"></a>Способы устранения 

Чтобы создать новую очередь Yarn и выполнить балансировку выделения емкости для всех очередей, выполните следующие шаги с помощью Ambari. 

В этом примере емкость двух существующих очередей (очереди по умолчанию и очереди thriftsvr) изменяется с 50 % на 25 %, что позволяет обеспечить для новой очереди (spark) емкость 50 %.
| Очередь | Емкость | Макс. Емкость |
| --- | --- | --- | --- |
| по умолчанию | 25 % | 50 % |
| thrftsvr | 25 % | 50 % |
| spark | 50 % | 50 % |
1. Щелкните значок "Просмотры Ambari" в виде сетки, а затем выберите **Yarn Queue Manager** (Диспетчер очередей Yarn).

    ![Значок "Просмотры Ambari"](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. Выберите очередь **по умолчанию**.

    ![Выбор очереди по умолчанию](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. Измените **емкость** с 50 % на 25 % для очереди **по умолчанию**, а емкость для очереди **thriftsvr** на 25 %.

    ![Изменение емкости до 25 % для очередей thriftsvr и по умолчанию](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. Нажмите кнопку **Добавить очередь**, чтобы создать новую очередь.

    ![Кнопка добавления очереди](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. Присвойте имя новой очереди.

    ![Имя Spark для новой очереди](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. Оставьте значение ее **емкости** 50 %, а затем нажмите кнопку **Действия**.

    ![Кнопка "Действия"](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. Выберите **Save and Refresh Queues** (Сохранить и обновить очереди).

    ![Команда сохранения и обновления очередей](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

После применения эти изменения сразу же появятся в пользовательском интерфейсе планировщика Yarn.

### <a name="further-reading"></a>Дополнительные материалы

- [Планировщик емкости Yarn](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Как скачать журналы Yarn из кластера?

#### <a name="issue"></a>Проблема

Необходимо скачать журналы основного контейнера и других контейнеров приложения Yarn из кластера HDInsight.  

#### <a name="resolution-steps"></a>Способы устранения 

1. Подключитесь к кластеру HDInsight с помощью клиента Secure Shell (SSH) (ознакомьтесь с дополнительными материалами ниже).
1. Получите список всех идентификаторов текущих выполняемых приложений Yarn с помощью следующей команды:

```apache
yarn top
```
Идентификаторы приложений, журналы которых нужно скачать, перечислены в столбце `APPLICATIONID`.

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

Скачайте журналы для всех основных контейнеров приложения Yarn с помощью следующей команды:
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

Будет создан файл журнала с именем `amlogs.txt` в текстовом формате. 

Скачайте журналы только для последнего основного контейнера приложения Yarn с помощью следующей команды:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

Будет создан файл журнала с именем `latestamlogs.txt` в текстовом формате. 

Скачайте журналы для первых двух основных контейнеров приложения Yarn с помощью следующей команды:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

Будет создан файл журнала с именем `first2amlogs.txt` в текстовом формате. 

Скачайте все журналы контейнеров приложения Yarn с помощью следующей команды:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

Будет создан файл журнала с именем `logs.txt` в текстовом формате. 

Скачайте журнал контейнера Yarn для определенного контейнера с помощью следующей команды:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

Будет создан файл журнала с именем `containerlogs.txt` в текстовом формате.

#### <a name="further-readings"></a>Дополнительные материалы

- [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [APACHE HADOOP YARN – CONCEPTS AND APPLICATIONS](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/) (Apache Hadoop Yarn: приложения и основные понятия)









