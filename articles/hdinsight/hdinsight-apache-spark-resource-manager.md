<properties 
	pageTitle="Использование диспетчера ресурсов для выделения ресурсов для кластера Apache Spark в HDInsight | Microsoft Azure" 
	description="Узнайте о том, как использовать диспетчер ресурсов для кластера Spark в HDInsight для повышения производительности." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="nitinme"/>


# Управление ресурсами кластера Apache Spark в HDInsight Linux (предварительная версия)

Из этой статьи вы узнаете, как получать доступ к разным интерфейсам, связанным с кластером Spark, включая пользовательский интерфейс Ambari, пользовательский интерфейс YARN и сервер журнала Spark. Также вы узнаете, как настроить конфигурацию кластера для оптимальной производительности.

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Как запустить веб-интерфейс Ambari?

1. На начальной панели [портала Azure](https://ms.portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Можно также перейти к кластеру в разделе **Просмотреть все** > **Кластеры HDInsight**. 
 
2. В колонке кластера Spark нажмите **Панель мониторинга**. При появлении запроса введите учетные данные администратора для кластера Spark.

	![Запуск Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Начало работы с диспетчером ресурсов")

3. В результате должен запуститься веб-интерфейс Ambari, как показано ниже.

	![Веб-интерфейс Ambari](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Веб-интерфейс Ambari")

## Как запустить сервер журнала Spark?

1. На начальной панели [портала Azure](https://ms.portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели).

2. В разделе **Быстрые ссылки** колонке кластера щелкните элемент **Панель мониторинга кластера**. В колонке **Панель мониторинга кластера** щелкните **Сервер журнала Spark**.

	![Сервер журнала Spark](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Сервер журнала Spark")

	При появлении запроса введите учетные данные администратора для кластера Spark.


## Как запустить пользовательский интерфейс Yarn?

Вы можете использовать пользовательский интерфейс YARN для мониторинга приложений, которые выполняются в кластере Spark в настоящее время.

1. В колонке кластера щелкните **Панель мониторинга кластера**, а затем выберите пункт **YARN**.

	![Запуск пользовательского интерфейса YARN](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png)

	>[AZURE.TIP] Также пользовательский интерфейс YARN можно открыть из пользовательского интерфейса Ambari. Чтобы открыть пользовательский интерфейс Ambari, выберите в колонке кластера **Панель мониторинга кластера**, а затем щелкните **Панель мониторинга кластера HDInsight**. В пользовательском интерфейсе Ambari щелкните **YARN**, затем — **Быстрые ссылки**. Щелкните активный менеджер ресурсов и нажмите кнопку **Пользовательский интерфейс диспетчера ресурсов**.

## Какая конфигурация кластера будет оптимальной для запуска приложений Spark?

В зависимости от требований приложения можно изменять три основных параметра Spark: `spark.executor.instances`, `spark.executor.cores` и `spark.executor.memory`. Исполнитель — это процесс, запущенный для приложения Spark. Он выполняется на рабочем узле и отвечает за выполнение задач этого приложения. Число исполнителей по умолчанию и размеры исполнителя для каждого кластера определяются с учетом числа рабочих узлов и размера каждого рабочего узла. Эти значения хранятся в файле `spark-defaults.conf` на головных узлах кластера.

Эти три параметра конфигурации можно настроить на уровне кластера (для всех приложений, работающих в кластере) или для каждого отдельного приложения.

### Изменение параметров с помощью пользовательского интерфейса Ambari

1. В пользовательском интерфейсе Ambari щелкните **Spark**, выберите пункт **Конфигурации** и разверните категорию **Custom spark-defaults**.

	![Изменение параметров с помощью Ambari](./media/hdinsight-apache-spark-resource-manager/set-parameters-using-ambari.png)

2. Значения по умолчанию позволяют запустить в кластере одновременно четыре приложения Spark. Вы можете изменять эти значения из пользовательского интерфейса, как показано ниже.

	![Изменение параметров с помощью Ambari](./media/hdinsight-apache-spark-resource-manager/set-executor-parameters.png)

3. Чтобы сохранить изменения конфигурации, нажмите кнопку **Сохранить**. В верхней части страницы вы увидите предложение перезапустить все используемые службы. Щелкните **Перезапустить**.

	![Перезапуск служб](./media/hdinsight-apache-spark-resource-manager/restart-services.png)


### Изменение параметров для приложения, запущенного в записной книжке Jupyter

Чтобы изменить конфигурацию для приложений, запущенных в записной книжке Jupyter, можно использовать волшебную команду `%%configure`. Желательно вносить такие изменения в начале приложения, перед запуском первой ячейки кода. Это гарантирует, что конфигурация будет применена к сеансу Livy, когда он будет создан. Если вы хотите изменить конфигурацию на более позднем этапе выполнения приложения, следует использовать параметр `-f`. Но при этом будут потеряны все результаты, полученные в приложении.

В следующем фрагменте показано, как изменить конфигурацию для приложения, работающего в Jupyter.

	%%configure 
	{"executorMemory": "3072M", "executorCores": 4, “numExecutors”:10}

Параметры конфигурации следует передавать в виде строки JSON, расположенной сразу после команды magic, как показано в столбце примера.

### Изменение параметров для приложения, отправленного с помощью spark-submit

Следующая команда демонстрирует, как можно изменять параметры конфигурации для приложения пакетной службы, отправленного с помощью `spark-submit`.

	spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### Изменение параметров для приложения, отправленного с помощью cURL

Следующая команда демонстрирует, как можно изменять параметры конфигурации для приложения пакетной службы, отправленного с помощью cURL.

	curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### Как изменить эти параметры на сервере Thrift Spark?

Сервер Thrift Spark предоставляет доступ JDBC и ODBC к кластеру Spark. Он используется для обслуживания запросов к службе Spark SQL. Разные средства, такие как Power BI, Tableau и др., используют протокол ODBC для обмена данными с сервером Thrift Spark и выполнения запросов Spark SQL в виде приложения Spark. Когда вы создаете кластер Spark, запускаются два экземпляра сервера Thrift Spark, по одному на каждый головной узел. Каждый сервер Thrift Spark отображается в пользовательском интерфейсе YARN как приложение Spark.

Сервер Thrift Spark использует динамическое выделение исполнителей Spark, поэтому `spark.executor.instances` не используется. Вместо этого сервер Thrift Spark использует `spark.dynamicAllocation.minExecutors` и `spark.dynamicAllocation.maxExecutors`, чтобы указать число исполнителей. Параметры конфигурации `spark.executor.cores` и `spark.executor.memory` используются для изменения размера исполнителя. Все эти параметры вы можете изменять, как показано ниже.

* Разверните категорию **Advanced spark-thrift-sparkconf**, чтобы изменить параметры `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` и `spark.executor.memory`.

	![Настройка сервера Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-1.png)

* Разверните категорию **Custom spark-thrift-sparkconf**, чтобы изменить параметр `spark.executor.cores`.

	![Настройка сервера Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-2.png)

### Как можно изменить память драйверов для сервера Thrift Spark?

Память драйверов сервера Thrift Spark настроена так, что она использует 25 % от размера ОЗУ головного узла, при условии, что общий объем ОЗУ головного узла превышает 14 ГБ. Конфигурацию памяти драйверов можно изменить с помощью пользовательского интерфейса Ambari, как показано ниже.

* В пользовательском интерфейсе Ambari щелкните **Spark**, нажмите **Конфигурации**, разверните категорию **Advanced spark-env** и введите значение для параметра **spark\_thrift\_cmd\_opts**.

	![Настройка памяти сервера Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-ram.png)

## Я не использую бизнес-аналитику с кластером Spark. Как получить ресурсы обратно?

Так как мы используем динамическое выделение Spark, сервер Thrift потребляет только ресурсы, предназначенные для двух главных серверов приложений. Чтобы освободить эти ресурсы, следует остановить службы сервера Thrift, запущенные в кластере.

1. В пользовательском интерфейсе Ambari на панели слева щелкните **Spark**.

2. На следующей странице щелкните **Серверы Thrift Spark**.

	![Перезапуск сервера Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-1.png)

3. Вы увидите два головных узла, на которых запущен сервер Thrift Spark. Выберите один из этих головных узлов.

	![Перезапуск сервера Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-2.png)

4. На следующей странице перечислены все службы, запущенные на выбранном головном узле. Нажмите в этом списке кнопку раскрывающегося списка рядом с сервером Thrift Spark, затем нажмите кнопку **Остановить**.

	![Перезапуск сервера Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-3.png)

5. Повторите эти действия на другом головном узле.


## Мои записные книжки Jupyter работают не так, как ожидалось. Как я могу перезапустить службу?

1. Запустите веб-интерфейс Ambari, как показано выше. В левой области навигации щелкните **Jupyter**, **Service Actions** (Действия службы) и затем **Перезапустить все**. При этом служба Jupyter запускается на всех головных узлах.

	![Перезапуск Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Перезапуск Jupyter")

	


## <a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Средства и расширения

* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0601_2016-->