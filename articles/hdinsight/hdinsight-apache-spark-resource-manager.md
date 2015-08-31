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
	ms.date="07/31/2015" 
	ms.author="nitinme"/>


# Управление ресурсами кластера Apache Spark в Azure HDInsight

Диспетчер ресурсов является компонентом панели мониторинга кластера Spark, с помощью которого можно управлять ресурсами, например ядрами и объемом ОЗУ, используемыми каждым приложением, которое выполняется в кластере.

## <a name="launchrm"></a>Как запустить диспетчер ресурсов?

1. На начальной панели [портала предварительной версии Azure](https://ms.portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Можно также перейти к кластеру в разделе **Просмотреть все** > **Кластеры HDInsight**. 
 
2. В колонке кластера Spark нажмите **Панель мониторинга**. При появлении запроса введите учетные данные администратора для кластера Spark.

	![Запуск диспетчера ресурсов](./media/hdinsight-apache-spark-resource-manager/HDI.Cluster.Launch.Dashboard.png "Начало работы с диспетчером ресурсов")

##<a name="scenariosrm"></a>Как устранить эти проблемы, используя диспетчер ресурсов?

Ниже перечислены наиболее распространенные сценарии, с которыми вы можете столкнуться при работе с кластером Spark, а также инструкции по устранению этих проблем, используя диспетчер ресурсов.

### Кластер Spark в HDInsight медленно работает

Кластер Apache Spark в HDInsight предназначен для работы в многопользовательском режиме, поэтому ресурсы разделены между несколькими компонентами (записными книжками, сервером заданий, и т. д.). Это позволяет использовать все компоненты Spark параллельно, не беспокоясь о том, что какому-либо компоненту не удается получить ресурсы для запуска, но при этом каждый компонент будет работать медленнее, поскольку ресурсы фрагментированы. Это можно настроить в зависимости от ваших потребностей.


### Ноутбук Jupyter использовать только с кластером Spark. Как мне выделить все ресурсы для этого интерфейса?

1. В разделе **Панель мониторинга Spark** щелкните вкладку **Пользовательский интерфейс Spark**, чтобы определить максимальное количество ядер и максимальный объем ОЗУ, которые можно выделить для приложений.

	![Выделение ресурсов](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Resource.png "Поиск ресурсов, выделенных для кластера Spark")

	На снимке экрана ниже видно, что максимальное количество ядер, которые можно выделить, составляет 7 (всего 8 ядер, из которых 1 занято), а максимальный объем ОЗУ, который можно выделить, — 9 ГБ (всего 12 ГБ ОЗУ, из которых 2 ГБ следует зарезервировать для системы и 1 ГБ для других приложений).

	Следует также учитывать любые выполняющиеся приложения. Просмотреть запущенные приложения можно на вкладке **Пользовательский интерфейс Spark**.

	![Выполняющиеся приложения](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Running.Apps.png "приложения, выполняемые на кластере;")

	
2. На панели мониторинга Spark в HDInsight щелкните вкладку **Диспетчер ресурсов** и задайте значения для параметров **Число ядер приложения по умолчанию** и **Память исполнителя по умолчанию на каждый рабочий узел**. Для других свойств задайте значение 0.

	![Выделение ресурсов](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Allocate.Resources.png "Выделение ресурсов для приложений")

### Я не использую средства бизнес-аналитики с кластером Spark. Как мне вернуть ресурсы? 

Укажите для параметров числа ядер сервера Thrift и памяти исполнителя сервера Thrift значение 0. Когда серверу Thrift не выделены ядра или память, он переходит в состояние **ОЖИДАНИЕ**.

![Выделение ресурсов](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.No.Thrift.png "Серверу Thrift не выделены ресурсы")

##<a name="seealso"></a>См. также:

* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Подготовка Spark в кластере HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Создание приложений машинного обучения с помощью Spark в HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark в HDInsight для создания приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=August15_HO8-->