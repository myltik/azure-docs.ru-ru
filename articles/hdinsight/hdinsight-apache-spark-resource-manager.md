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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Управление ресурсами кластера Apache Spark в Azure HDInsight (Linux)

Spark в Azure HDInsight (Linux) предоставляет веб-интерфейс Ambari для управления ресурсами кластера и мониторинга его работоспособности. Кроме того, вы можете использовать сервер журнала Spark для отслеживания приложений, которые завершили выполнение в кластере. Вы можете использовать пользовательский интерфейс YARN для мониторинга приложений, которые выполняются в кластере в настоящее время. В этой статье описано, как получить доступ к этим пользовательским интерфейсам и как с их помощью выполнять некоторые базовые задачи управления ресурсами.

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Как запустить веб-интерфейс Ambari?

1. На начальной панели [портала предварительной версии Azure](https://ms.portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, выбрав пункты **Просмотреть все** и **Кластеры HDInsight**. 
 
2. В колонке кластера Spark нажмите **Панель мониторинга**. При появлении запроса введите учетные данные администратора для кластера Spark.

	![Запуск Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Начало работы с диспетчером ресурсов")

3. В результате должен запуститься веб-интерфейс Ambari, как показано ниже.

	![Веб-интерфейс Ambari](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Веб-интерфейс Ambari")

## Как запустить сервер журнала Spark?

1. На начальной панели [портала предварительной версии Azure](https://ms.portal.azure.com/) щелкните плитку кластера Spark (если она закреплена на начальной панели).

2. В разделе **Быстрые ссылки** колонке кластера щелкните элемент **Панель мониторинга кластера**. В колонке **Панель мониторинга кластера** щелкните **Сервер журнала Spark**.

	![Сервер журнала Spark](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Сервер журнала Spark")

	При появлении запроса введите учетные данные администратора для кластера Spark.


## Как запустить пользовательский интерфейс Yarn?

Вы можете использовать пользовательский интерфейс YARN для мониторинга приложений, которые выполняются в кластере Spark в настоящее время. Перед обращением к пользовательскому интерфейсу YARN следует включить туннелирование SSH в кластер. Инструкции см. в статье [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari](hdinsight-linux-ambari-ssh-tunnel.md)

1. Запустите веб-интерфейс Ambari, как показано выше.

2. В веб-интерфейсе Ambari выберите YARN в списке в левой части страницы.

3. 3\. После появления информации о службе YARN выберите **Быстрые ссылки**. Появится список головных узлов кластера. Выберите один из головных узлов, а затем выберите **Пользовательский интерфейс ResourceManager**.

	![Запуск пользовательского интерфейса YARN](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "Запуск пользовательского интерфейса YARN")

4. В результате должен быть запущен пользовательский интерфейс YARN со страницей, аналогичной следующей:

	![Пользовательский интерфейс YARN](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "Пользовательский интерфейс YARN")

##<a name="scenariosrm"></a>Как управлять ресурсами с помощью веб-интерфейса Ambari?

Ниже перечислены наиболее распространенные сценарии, с которыми вы можете столкнуться при работе с кластером Spark, а также инструкции по устранению этих проблем с помощью веб-интерфейса Ambari.

### Я не использую бизнес-аналитику с кластером Spark. Как получить ресурсы обратно?

1. Запустите веб-интерфейс Ambari, как показано выше. В левой области навигации щелкните **Spark**, а затем щелкните **Конфигурации**.

2. Найдите в списке доступных конфигураций **Custom spark-thrift-sparkconf** и измените значение **spark.executor.memory** и **spark.drivers.core** на **0**.

	![Ресурсы для бизнес-аналитики](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "Ресурсы для бизнес-аналитики")

3. Щелкните **Сохранить**. Введите описание для внесенных изменений и нажмите кнопку **Сохранить** еще раз.

4. В верхней части страницы должен появиться запрос на перезапуск службы Spark. Щелкните элемент **Перезапустить** чтобы изменения вступили в силу.


### Мои записные книжки Jupyter работают не так, как ожидалось. Как я могу перезапустить службу?

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



[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0211_2016-->