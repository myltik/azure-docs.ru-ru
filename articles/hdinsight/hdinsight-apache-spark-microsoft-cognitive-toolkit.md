---
title: "Использование Microsoft Cognitive Toolkit с Azure HDInsight Spark для глубокого обучения | Документация Майкрософт"
description: "Узнайте, как можно применить обученную модель обучения Microsoft Cognitive Toolkit к набору данных с помощью API Python для Spark в кластере Azure HDInsight Spark."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 2dc1f8ae8e0a2986806445dc9852a54ceaaef5a4
ms.lasthandoff: 04/21/2017


---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Использование модели глубокого обучения Microsoft Cognitive Toolkit в кластере Azure HDInsight Spark

Ниже перечислены действия, которые вы выполните в этой статье.

1. Запуск пользовательского сценария для установки Microsoft Cognitive Toolkit в кластере Azure HDInsight Spark.

2. Передача Jupyter Notebook в кластер Spark для применения обученной модели глубокого обучения Microsoft Cognitive Toolkit к файлам в учетной записи хранилища BLOB-объектов Azure с помощью [API Python для Spark (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html).

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Ознакомьтесь со страницей [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/free).

* **Кластер Azure HDInsight Spark**. В этой статье создайте кластер Spark 2.0. Инструкции см. в разделе [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Каково предназначение этой последовательности решения?

Для описания этого решения используются данная статья и элемент Jupyter Notebook, переданный в рамках данного руководства. Ниже перечислены действия, которые вы выполните в этой статье.

* Запуск действия сценария в кластере HDInsight Spark для установки Microsoft Cognitive Toolkit и пакетов Python.
* Передача элемента Jupyter Notebook, запускающего решение в кластере HDInsight Spark.

Перечисленные ниже оставшиеся шаги приведены в описании Jupyter Notebook.

- Загрузка примеров изображений в устойчивый распределенный набор данных Spark (RDD).
   - Загрузка модулей и определение предустановок.
   - Скачивание набора данных локально в кластер Spark.
   - Преобразование набора данных в RDD.
- Оценка изображений с помощью обученной модели Cognitive Toolkit.
   - Скачивание обученной модели Cognitive Toolkit в кластер Spark.
   - Определение функций, используемых рабочими узлами.
   - Оценка изображений на рабочих узлах.
   - Анализ точности модели.


## <a name="install-microsoft-cognitive-toolkit"></a>Установка Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit в кластере Spark можно установить с помощью действия сценария. Действие сценария использует пользовательские скрипты для установки компонентов в кластере, которые по умолчанию недоступны. Можно использовать пользовательский сценарий с портала Azure, воспользовавшись пакетом SDK .NET для HDInsight или Azure PowerShell. Этот сценарий можно также использовать для установки данного набора средств при создании кластера или после его подготовки и запуска. 

В этой статье мы используем портал для установки набора средств после того, как кластер был создан. Другие способы выполнения пользовательского сценария описаны в разделе [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Использование портала Azure

Инструкции по использованию портала Azure для выполнения действия сценария см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Обязательно укажите приведенные ниже данные для установки Microsoft Cognitive Toolkit.

* Укажите значение имени действия сценария.

* В поле **URI bash-скрипта** введите `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Настройте выполнение сценария только на головном и рабочем узлах и снимите все остальные флажки.

* Щелкните **Создать**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Передача Jupyter Notebook в кластер Azure HDInsight Spark

Чтобы использовать Microsoft Cognitive Toolkit с кластером Azure HDInsight Spark, необходимо загрузить Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** в кластер Azure HDInsight Spark. Этот элемент Notebook можно найти на сайте GitHub: [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Клонируйте репозиторий GitHub [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Инструкции по клонированию приведены в разделе [Cloning a repository](https://help.github.com/articles/cloning-a-repository/) (Клонирование репозитория).

2. На портале Azure откройте колонку кластера Spark, который уже подготовлен, щелкните **Панель мониторинга кластера**, а затем щелкните **Записная книжка Jupyter**.

    Можно также запустить Jupyter Notebook, перейдя по URL-адресу `https://<clustername>.azurehdinsight.net/jupyter/`. Замените \<clustername> именем кластера HDInsight.

3. В Jupyter Notebook нажмите кнопку **Upload** (Передать) в правом верхнем углу, а затем перейдите к расположению, в которое вы клонировали репозиторий GitHub.

    ![Передача Jupyter Notebook в кластер Azure HDInsight Spark](./media/hdinsight-apache-spark-microsoft-cognitive-toolkit.md/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Upload Jupyter notebook to Azure HDInsight Spark cluster")

4. Еще раз нажмите кнопку **Upload** (Передать).

5. После передачи элемента Notebook щелкните его имя, а затем следуйте отображаемым в Notebook указаниям по загрузке набора данных и выполните задания руководства.

## <a name="see-also"></a>См. также
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Analyze Application Insights telemetry logs with Spark on HDInsight (Анализ журналов телеметрии Application Insights с помощью Spark в HDInsight)](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

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

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

