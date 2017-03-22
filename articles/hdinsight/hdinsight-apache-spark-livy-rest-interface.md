---
title: "Удаленная отправка заданий в кластер Spark в Azure HDInsight с помощью Livy | Документация Майкрософт"
description: "Узнайте, как использовать Livy с кластерами HDInsight для удаленной отправки заданий Spark."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 3c349aecc87e28275045828a84e0ea3f89400b9e
ms.lasthandoff: 01/24/2017


---
# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-using-livy"></a>Удаленная отправка заданий Spark в кластер Apache Spark в HDInsight с помощью Livy

Кластер Apache Spark в Azure HDInsight включает Livy — интерфейс REST для удаленной отправки заданий в кластер Spark. Подробную документацию см. в статье о [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Вы можете использовать Livy для выполнения интерактивных оболочек Spark или отправки пакетных заданий для запуска в кластере Spark. В статье рассматривается использование Livy для отправки пакетных заданий. В представленном ниже синтаксисе используется Curl, позволяющий REST вызывать конечную точку Livy.

**Предварительные требования:**

Необходимо следующее:

* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job"></a>Отправка пакетного задания
Перед отправкой пакетного задания необходимо загрузить JAR-файл приложения в хранилище кластеров, связанное с соответствующим кластером. Вы можете использовать для этого служебную программу командной строки [**AzCopy**](../storage/storage-use-azcopy.md). Кроме того, для отправки данных вы можете использовать множество других клиентов. Дополнительные сведения о них см. в статье [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Примеры**:

* Если JAR-файл находится в хранилище кластеров (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Если имя JAR-файла и имя класса необходимо передать в составе входного файла (в данном примере input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Получение сведений о пакетах, выполняемых в кластере
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Примеры**:

* Если вы хотите получить все пакеты, выполняемые в кластере:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Если вы хотите получить пакет с определенным идентификатором
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-batch-job"></a>Удаление пакетного задания
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Пример**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Livy и высокая доступность
Livy обеспечивает высокую доступность заданий Spark, выполняемых в кластере. Вот несколько примеров.

* Если после удаленной отправки задания в кластер Spark служба Livy перестает работать, задание продолжает выполняться в фоновом режиме. Выполняя резервное копирование, служба Livy восстанавливает состояние задания и отправляет отчет.
* Служба Livy в серверной части обеспечивает работу записных книжек Jupyter для HDInsight. Если записная книжка выполняет задание Spark, а служба Livy перезапускается, записная книжка продолжает выполнять ячейки кода. 

## <a name="show-me-an-example"></a>Показать пример
В этом разделе мы рассмотрим примеры использования Livy для отправки приложений Spark, проследим за ходом выполнения приложения, а затем удалим задание. В примере используется приложение, разработанное в статье [Создание автономного приложения Scala для работы в кластере HDInsight Spark (Linux)](hdinsight-apache-spark-create-standalone-application.md). В описанных ниже действиях предполагается следующее.

* Вы уже скопировали JAR-файл приложения в учетную запись хранения, связанную с кластером.
* На компьютере, где выполняются эти действия, установлен CuRL.

Выполните следующие действия.

1. Для начала убедитесь в том, что в кластере выполняется Livy. Для этого получите список выполняемых пакетов. Если Livy используется для выполнения задания в первый раз, должно появиться значение ноль.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Должен отобразиться результат, аналогичный приведенному ниже:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Обратите внимание на то, что последняя строка выходных данных содержит значение **total:0**, означающее, что никакие пакеты не выполняются.
2. Теперь отправим пакетное задание. В приведенном ниже фрагменте кода для передачи имени JAR-файла и имени класса в качестве параметров используется входной файл (input.txt). Используйте этот вариант, если данные действия выполняются на компьютере Windows.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Параметры в файле **input.txt** определяются следующим образом:
   
        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Должен отобразиться результат, аналогичный приведенному ниже:
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Обратите внимание на то, что последняя строка выходных данных содержит значение **state:starting**. Кроме того, она включает параметр **id:0** — это идентификатор пакета.
3. Теперь вы можете извлечь состояние конкретного пакета по идентификатору соответствующего пакета.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Должен отобразиться результат, аналогичный приведенному ниже:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Теперь выходные данные содержат параметр **state:success**, означающий, что задание выполнено.
4. При необходимости пакет можно удалить.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Должен отобразиться результат, аналогичный приведенному ниже:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Последняя строка выходных данных показывает, что пакет удален. Если задание удаляется в процессе выполнения, оно фактически аннулируется. Если задание удаляется после завершения (независимо от успешности выполнения), удаляются все данные об этом задании.

## <a name="using-livy-on-hdinsight-35-spark-clusters"></a>Использование Livy в кластерах HDInsight 3.5 Spark

Кластер HDInsight 3.5 по умолчанию запрещает использование локальных путей для доступа к примерам файлов данных или JAR-файлам. Мы рекомендуем использовать путь `wasb://` для доступа к JAR-файлам или примерам файлов данных из кластера. Если вы хотите использовать локальный путь, необходимо соответствующим образом обновить конфигурацию Ambari. Для этого выполните следующие действия:

1. Перейдите на портал Ambari для кластера. Веб-интерфейс Ambari доступен по адресу https://**CLUSTERNAME**.azurehdidnsight.net, где CLUSTERNAME — это имя вашего кластера HDInsight.

2. В левой области навигации щелкните **Livy**, а затем щелкните **Configs** (Конфигурации).

3. В разделе **livy-default** добавьте имя свойства `livy.file.local-dir-whitelist` и присвойте ему значение **"/"**, если вы хотите разрешить полный доступ к файловой системе. Если вы хотите разрешить доступ только к конкретному каталогу, то в качестве значения свойства укажите путь к этому каталогу.


## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)


