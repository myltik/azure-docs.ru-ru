---
title: "Устранение неполадок со Spark в Azure HDInsight | Документация Майкрософт"
description: "Ознакомьтесь с ответами на часто задаваемые вопросы о Spark на платформе Azure HDInsight."
keywords: "Azure HDInsight, Spark, часто задаваемые вопросы, руководство по устранению неполадок, распространенные проблемы, конфигурация приложения, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 7655acd689c3f5b869eb0ddb5f186cff032ca8b6
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017


---

# <a name="spark-troubleshooting"></a>Устранение неполадок со Spark

В этой статье описываются основные проблемы и способы их устранения при работе с полезными данными Spark в Apache Ambari.

## <a name="how-do-i-configure-a-spark-application-through-ambari-on-clusters"></a>Как настроить приложение Spark с помощью Ambari в кластерах?

### <a name="issue"></a>Проблема

Необходимо настроить в Ambari объем памяти и количество ядер, которые может использовать приложение Spark.  

### <a name="resolution-steps"></a>Способы устранения 

Значения конфигурации этой процедуры предварительно заданы в кластерах HDInsight Spark. Ознакомьтесь с ответом на проблему [сбоя приложения Spark из-за ошибки OutOfMemoryError](#why-did-my-spark-application-fail-with-an-outofmemoryerror), чтобы определить, какие конфигурации Spark нужно настроить и с какими значениями.

1. Выберите **Spark2** из списка кластеров.

    ![Выбор кластера из списка](media/hdinsight-troubleshoot-spark/update-config-1.png)

1. Выберите вкладку **Configs** (Конфигурации).

    ![Вкладка конфигураций](media/hdinsight-troubleshoot-spark/update-config-2.png)

1. В списке конфигураций выберите **Custom-spark2-defaults**.

    ![Выбор Custom-spark2-defaults](media/hdinsight-troubleshoot-spark/update-config-3.png)

1. Найдите параметр значения, который необходимо настроить, например **spark.executor.memory**. В этом случае значение 4608m слишком высокое.

    ![Выбор поля spark.executor.memory](media/hdinsight-troubleshoot-spark/update-config-4.png)

1. Установите рекомендуемое значение. В этом случае для этого параметра мы рекомендуем использовать 2048.

    ![Изменение значения на 2048m](media/hdinsight-troubleshoot-spark/update-config-5.png)

1. Сохраните это значение параметра, а затем сохраните конфигурацию. 

    Нажмите кнопку **Сохранить** на панели инструментов.

    ![Сохранение параметра и конфигурации](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    Если нужно будет пересмотреть какую-либо конфигурацию, вы получите оповещение. Запишите их значения, а затем нажмите кнопку **Proceed Anyway** (Продолжить). 

    ![Кнопка Proceed Anyway (Продолжить)](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    Запишите примечание об изменениях конфигурации и нажмите кнопку **Сохранить**.

    ![Примечание об изменениях](media/hdinsight-troubleshoot-spark/update-config-6c.png)

1. При каждом сохранении конфигурации вам будет предложено перезапустить службу. Щелкните **Перезапустить**.

    ![Кнопка "Перезапустить"](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    Подтвердите перезапуск.

    ![Кнопка подтверждения перезапуска](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    Вы можете просмотреть запущенные процессы.

    ![Просмотр запущенных процессов](media/hdinsight-troubleshoot-spark/update-config-7c.png)

1. Вы также можете добавить конфигурации. В списке конфигураций выберите **Custom-spark2-defaults**, как вы делали на шаге 3, а затем выберите **Добавить свойство**.

    ![Кнопка добавления свойства](media/hdinsight-troubleshoot-spark/update-config-8.png)

1. Определите новое свойство. Вы можете определить отдельное свойство с помощью диалогового окна для определенных параметров, например тип данных, или вы можете определить несколько свойств с одним определением на строку. 

    В этом примере свойство **spark.driver.memory** определяется со значением 4 ГБ.

    ![Определение нового свойства](media/hdinsight-troubleshoot-spark/update-config-9.png)

1. Сохраните конфигурацию и перезапустите службу, как описано на шагах 6 и 7.

Эти изменения применяются на уровне кластера, но их можно переопределить во время фактического времени отправки задания Spark.

### <a name="further-reading"></a>Дополнительные материалы

[Отправка задания Spark в кластерах HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters"></a>Как настроить приложение Spark с помощью записной книжки Jupyter в кластерах?

### <a name="issue"></a>Проблема

Необходимо настроить объем памяти и количество ядер, которые может использовать приложение Spark при использовании записной книжки Jupyter в кластерах HDInsight. 

1. Ознакомьтесь с ответом на проблему [сбоя приложения Spark из-за ошибки OutOfMemoryError](#spark-application-failure-outofmemory), чтобы определить, какие конфигурации Spark нужно настроить и с какими значениями.
1.  Укажите конфигурации Spark в допустимом формате JSON в первой ячейке записной книжки Jupyter после директивы %%configure (при необходимости измените фактические значения): 

>![Добавление конфигурации](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="further-reading"></a>Дополнительные материалы

[Отправка задания Spark в кластерах HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-livy-on-clusters"></a>Как настроить приложение Spark с помощью LIVY в кластерах?

### <a name="issue"></a>Проблема

Во время отправки с помощью LIVY необходимо настроить объем памяти и количество ядер, которые может использовать приложение Spark в кластерах HDInsight. 

1. Ознакомьтесь с ответом на проблему [сбоя приложения Spark из-за ошибки OutOfMemoryError](#spark-application-failure-outofmemory), чтобы определить, какие конфигурации Spark нужно настроить и с какими значениями.
1. Отправьте приложение Spark в LIVY с помощью клиента REST, например CURL, используя команду, подобную следующей (при необходимости измените фактические значения):

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

### <a name="further-reading"></a>Дополнительные материалы

[Отправка задания Spark в кластерах HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-spark-submit-on-clusters"></a>Как настроить приложение Spark с помощью spark-submit в кластерах?

### <a name="issue"></a>Проблема 

Во время отправки с помощью spark-submit необходимо настроить объем памяти и количество ядер, которые может использовать приложение Spark в кластерах HDInsight.

1. Ознакомьтесь с ответом на проблему [сбоя приложения Spark из-за ошибки OutOfMemoryError](#spark-application-failure-outofmemory), чтобы определить, какие конфигурации Spark нужно настроить и с какими значениями.
1. Запустите оболочку Spark с помощью команды, подобной следующей (при необходимости измените фактическое значение конфигураций): 

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="further-reading"></a>Дополнительные материалы

[Отправка задания Spark в кластерах HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)



## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Что вызывает в приложении Spark исключение OutOfMemoryError?

### <a name="error"></a>Ошибка:

В приложении Spark возникло исключение OutOfMemoryError.

### <a name="detailed-description"></a>Подробное описание

Сбой приложения Spark со следующими типами неперехваченных исключений.  
```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Возможные причины

Наиболее вероятной причиной этого исключения является нехватка памяти кучи, выделенной для виртуальной машины Java, которая запускается как исполнитель или драйвер в рамках приложения Spark. 

### <a name="resolution-steps"></a>Способы устранения

1. Определите максимальный объем данных, которые будет обрабатывать приложение Spark. Это значение можно предугадать, исходя из максимального размера входных данных, промежуточных данных, создаваемых при преобразовании входных данных, и выходных данных, получаемых при дальнейшем преобразовании промежуточных данных. Если изначально правильно предугадать объем невозможно, это можно сделать, используя циклический процесс. 
1. Убедитесь, что в кластере HDInsight, который должен использоваться, достаточно ресурсов, таких как память и количество ядер, для работы приложения Spark. Это можно определить, просмотрев в разделе Cluster Metrics (Метрики кластера) пользовательского интерфейса YARN кластера такие значения, как Memory Used (Используемая память) и Memory Total (Всего памяти), а также VCores Used (Используемые ядра VCore) и VCores Total (Всего ядер VCore).

1. Задайте для параметров конфигурации Spark соответствующие значения, не превышающие 90 % доступной памяти и количества ядер, отображающихся в YARN, но в пределах требований к объему памяти для приложения Spark: 

```apache
spark.executor.instances (Example: 8 for 8 executor count) 
spark.executor.memory (Example: 4g for 4 GB) 
spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
spark.executor.cores (Example: 2 for 2 cores per executor) 
spark.driver.memory (Example: 8g for 8GB) 
spark.driver.cores (Example: 4 for 4 cores)   
spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
```

Общий объем памяти, используемый всеми исполнителями = 
```apache
spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
```
Общий объем памяти, используемый драйвером = 
```apache
spark.driver.memory + spark.yarn.driver.memoryOverhead
```

### <a name="further-reading"></a>Дополнительные материалы

- [Раздел об управлении памятью Spark](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Статья об отладке приложения Spark в кластерах HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)










