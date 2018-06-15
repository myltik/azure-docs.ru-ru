---
title: Управление ресурсами для кластера Apache Spark в Azure HDInsight | Документы Майкрософт
description: Узнайте, как управлять ресурсами для кластеров Spark в Azure HDInsight для повышения производительности.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: afefec77d9d79ad97fa351bc8b0710894c242d76
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31520514"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Управление ресурсами для кластера Apache Spark в Azure HDInsight 

Узнайте, как получать доступ к разным интерфейсам, связанным с кластером Spark, включая пользовательский интерфейс Ambari, пользовательский интерфейс YARN и сервер журнала Spark, и как настроить конфигурацию кластера для оптимальной производительности.

**Предварительные требования:**

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Открытие веб-интерфейса Ambari

Apache Ambari используется для мониторинга кластера и внесения изменений в его конфигурацию. Дополнительные сведения см. в статье [Управление кластерами Hadoop в HDInsight с помощью портала Azure](../hdinsight-administer-use-portal-linux.md#open-the-ambari-web-ui).

## <a name="open-the-spark-history-server"></a>Открытие сервера журнала Spark

Сервер журнала Spark — это пользовательский веб-интерфейс для готовых и запущенных приложений Spark. Он представляет собой расширение для пользовательского веб-интерфейса Spark.

**Открытие пользовательского веб-интерфейса сервера журнала Spark**

1. Откройте кластер Spark на [портале Azure](https://portal.azure.com/). Дополнительные сведения см. в разделе [Отображение кластеров](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. В разделе **Быстрые ссылки** щелкните **Панель мониторинга кластера**, а затем — **Сервер журнала Spark**.

    ![Сервер журнала Spark](./media/apache-spark-resource-manager/launch-history-server.png "Сервер журнала Spark")

    При появлении запроса введите учетные данные администратора для кластера Spark. Чтобы открыть сервер журнала Spark, вы также можете перейти по следующему URL-адресу:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Замените <ClusterName> именем кластера Spark.

Пользовательский веб-интерфейс сервера журнала Spark выглядит примерно так:

![Сервер журнала HDInsight Spark](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Открытие пользовательского интерфейса YARN
Вы можете использовать пользовательский интерфейс YARN для мониторинга приложений, которые выполняются в кластере Spark в настоящее время.

1. Откройте кластер Spark на [портале Azure](https://portal.azure.com/). Дополнительные сведения см. в разделе [Отображение кластеров](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. В разделе **Быстрые ссылки** щелкните **Панель мониторинга кластера**, а затем — **YARN**.

    ![Запуск пользовательского интерфейса YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Также пользовательский интерфейс YARN можно открыть из пользовательского интерфейса Ambari. Чтобы открыть пользовательский интерфейс Ambari, щелкните **Панель мониторинга кластера**, а затем выберите **Панель мониторинга кластера HDInsight**. В пользовательском интерфейсе Ambari щелкните **YARN**, затем — **Quick Links** (Быстрые ссылки). Щелкните активный Resource Manager и щелкните **ResourceManager UI** (Пользовательский интерфейс Resource Manager).
   >
   >

## <a name="optimize-clusters-for-spark-applications"></a>Оптимизация кластеров для приложений Spark
В зависимости от требований приложения можно изменять три основных параметра Spark: `spark.executor.instances`, `spark.executor.cores` и `spark.executor.memory`. Исполнитель — это процесс, запущенный для приложения Spark. Он выполняется на рабочем узле и отвечает за выполнение задач этого приложения. Число исполнителей по умолчанию и размеры исполнителя для каждого кластера определяются с учетом числа рабочих узлов и размера каждого рабочего узла. Эти сведения хранятся в файле `spark-defaults.conf` на головных узлах кластера.

Эти три параметра конфигурации можно настроить на уровне кластера (для всех приложений, работающих в кластере) или для каждого отдельного приложения.

### <a name="change-the-parameters-using-ambari-ui"></a>Изменение параметров с помощью пользовательского интерфейса Ambari
1. В пользовательском интерфейсе Ambari щелкните **Spark**, выберите пункт **Конфигурации** и разверните категорию **Custom spark-defaults**.

    ![Изменение параметров с помощью Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Значения по умолчанию позволяют запустить в кластере одновременно четыре приложения Spark. Вы можете изменять эти значения из пользовательского интерфейса, как показано на следующем снимке экрана:

    ![Изменение параметров с помощью Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Чтобы сохранить изменения конфигурации, нажмите кнопку **Сохранить** . В верхней части страницы вы увидите предложение перезапустить все используемые службы. Щелкните **Перезапустить**.

    ![Перезапуск служб](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Изменение параметров для приложения, запущенного в записной книжке Jupyter
Чтобы изменить конфигурацию для приложений, запущенных в записной книжке Jupyter, можно использовать волшебную команду `%%configure` . Желательно вносить такие изменения в начале приложения, перед запуском первой ячейки кода. Это гарантирует, что конфигурация будет применена к сеансу Livy, когда он будет создан. Если вы хотите изменить конфигурацию на более позднем этапе выполнения приложения, следует использовать параметр `-f` . Но при этом будут потеряны все результаты, полученные в приложении.

В следующем фрагменте кода показано, как изменить конфигурацию для приложения, работающего в Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Параметры конфигурации следует передавать в виде строки JSON, расположенной сразу после команды magic, как показано в столбце примера.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Изменение параметров для приложения, отправленного с помощью spark-submit
Следующая команда демонстрирует, как можно изменять параметры конфигурации для приложения пакетной службы, отправленного с помощью `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Изменение параметров для приложения, отправленного с помощью cURL
В следующей команде показано, как изменить параметры конфигурации для приложения пакетной службы, отправленного с помощью cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Изменение этих параметров на сервере Thrift Spark
Сервер Thrift Spark предоставляет доступ JDBC и ODBC к кластеру Spark. Он используется для обслуживания запросов к службе Spark SQL. Разные средства, такие как Power BI, Tableau и др., используют протокол ODBC для обмена данными с сервером Thrift Spark и выполнения запросов Spark SQL в виде приложения Spark. Когда вы создаете кластер Spark, запускаются два экземпляра сервера Thrift Spark, по одному на каждый головной узел. Каждый сервер Thrift Spark отображается в пользовательском интерфейсе YARN как приложение Spark.

Сервер Thrift Spark использует динамическое выделение исполнителей Spark, поэтому `spark.executor.instances` не используется. Вместо этого сервер Thrift Spark использует `spark.dynamicAllocation.minExecutors` и `spark.dynamicAllocation.maxExecutors`, чтобы указать число исполнителей. Параметры конфигурации `spark.executor.cores` и `spark.executor.memory` используются для изменения размера исполнителя. Эти параметры можно изменить, как описано ниже.

* Разверните категорию **Advanced spark-thrift-sparkconf**, чтобы обновить параметры `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` и `spark.executor.memory`.

    ![Настройка сервера Thrift Spark](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Разверните категорию **Custom spark-thrift-sparkconf**, чтобы изменить параметр `spark.executor.cores`.

    ![Настройка сервера Thrift Spark](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Изменение памяти драйверов для сервера Thrift Spark
Память драйверов сервера Thrift Spark настроена так, что она использует 25 % от размера ОЗУ головного узла, при условии, что общий объем ОЗУ головного узла превышает 14 ГБ. Конфигурацию памяти драйверов можно изменить с помощью пользовательского интерфейса Ambari, как показано на снимке экрана ниже.

* В пользовательском интерфейсе Ambari щелкните **Spark**, нажмите **Конфигурации**, разверните категорию **Advanced spark-env** и введите значение для параметра **spark_thrift_cmd_opts**.

    ![Настройка памяти сервера Thrift Spark](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Освобождение кластерных ресурсов Spark
Так как используется динамическое выделение Spark, сервер Thrift потребляет только ресурсы, предназначенные для двух главных серверов приложений. Чтобы освободить эти ресурсы, следует остановить службы сервера Thrift, запущенные в кластере.

1. В пользовательском интерфейсе Ambari на панели слева щелкните **Spark**.
2. На следующей странице щелкните **Серверы Thrift Spark**.

    ![Перезапуск сервера Thrift](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Вы увидите два головных узла, на которых запущен сервер Thrift Spark. Выберите один из этих головных узлов.

    ![Перезапуск сервера Thrift](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. На следующей странице перечислены все службы, запущенные на выбранном головном узле. Нажмите в этом списке кнопку раскрывающегося списка рядом с сервером Thrift Spark, затем нажмите кнопку **Остановить**.

    ![Перезапуск сервера Thrift](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Повторите эти действия на другом головном узле.

## <a name="restart-the-jupyter-service"></a>Перезапуск службы Jupyter
Откройте пользовательский веб-интерфейс Ambari, как показано в начале статьи. В левой области навигации щелкните **Jupyter**, **Service Actions** (Действия службы), а затем — **Перезапустить все**. Служба Jupyter запустится на всех головных узлах.

![Перезапуск Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Перезапуск Jupyter")

## <a name="monitor-resources"></a>Мониторинг ресурсов
Откройте пользовательский интерфейс Yarn, как показано в начале статьи. В таблице метрик кластера в верхней части экрана проверьте значения столбцов **Memory Used** (Используемая память) и **Memory Total** (Всего памяти). Если эти два значения близки, то для запуска следующего приложения может не хватить ресурсов. То же самое относится к столбцам **VCores Used** (Используемые ядра VCore) и **VCores Total** (Всего ядер VCore). Кроме того, если в главном представлении есть приложение с состоянием **ACCEPTED** (Принято), которое не переходит в состояние **RUNNING** (Выполняется) или **FAILED** (Сбой), то это также может означать, что для его запуска недостаточно ресурсов.

![Ограничение ресурсов](./media/apache-spark-resource-manager/resource-limit.png "Ограничение ресурсов")

## <a name="kill-running-applications"></a>Завершение работы запущенных приложений
1. В пользовательском интерфейсе Yarn на левой панели щелкните **Running** (Выполняется). В списке выполняющихся приложений определите приложение, работу которого необходимо завершить, и щелкните **ID** (Идентификатор).

    ![Завершение работы приложения 1](./media/apache-spark-resource-manager/kill-app1.png "Завершение работы приложения 1")

2. В правом верхнем углу щелкните **Kill Application** (Завершить работу приложения), а затем нажмите кнопку **ОК**.

    ![Завершение работы приложения 2](./media/apache-spark-resource-manager/kill-app2.png "Завершение работы приложения 2")

## <a name="see-also"></a>См. также
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Для специалистов по анализу данных

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analyze Application Insights telemetry logs with Spark on HDInsight (Анализ журналов телеметрии Application Insights с помощью Spark в HDInsight)](apache-spark-analyze-application-insight-logs.md)
* [Использование Caffe в кластере Azure HDInsight Spark для распределенного глубокого обучения](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Для разработчиков Spark

* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)
