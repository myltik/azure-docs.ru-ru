---
title: "Использование внешних пакетов Python с элементами Jupyter Notebook в кластерах Apache Spark в HDInsight | Документация Майкрософт"
description: "Пошаговые инструкции по настройке элементов Jupyter Notebook с кластерами Spark HDInsight для использования внешних пакетов Python."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21978b71-eb53-480b-a3d1-c5d428a7eb5b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f782920ca8aeafa745e6588a71a3428a01676db3
ms.openlocfilehash: 288474a8fbd4ca41adb8a2525f3c9ffe970580ea


---
# <a name="use-external-python-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Использование внешних пакетов Python с элементами Jupyter Notebook в кластерах Apache Spark в HDInsight на платформе Linux

Узнайте, как с помощью действий сценария настроить кластер Apache Spark в HDInsight (Linux) для использования внешних, предоставленных сообществом пакетов **Python**, которые не включены в готовую версию кластера.

Полный список доступных пакетов можно найти в [указателе пакетов](https://pypi.python.org/pypi). Его также можно получить из других источников. Например, можно установить пакеты, предоставляемые посредством [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) или [conda-forge](https://conda-forge.github.io/feedstocks.html).

В этой статье вы узнаете, как установить в кластере пакет [TensorFlow](https://www.tensorflow.org/) с помощью действия сценария и использовать его посредством Jupyter Notebook.

## <a name="prerequisites"></a>Предварительные требования
Необходимо следующее:

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Если у вас еще нет кластера Spark в HDInsight на платформе Linux, можно выполнить действия сценария во время его создания. Обратитесь к документации по [использованию настраиваемых действий сценария](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Использование внешних пакетов с записными книжками Jupyter

1. На начальной панели [портала Azure](https://portal.azure.com/)щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, последовательно щелкнув **Просмотреть все** > **Кластеры HDInsight**.   

2. В колонке кластера Spark щелкните **Действия скрипта** в разделе **Использование**. Выполните настраиваемое действие, устанавливающее TensorFlow на головные узлы и рабочие узлы. Сценарий bash может быть основан на https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh. Обратитесь к документации по [использованию настраиваемых действий сценария](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > В кластере имеются два установленных компонента Python. Spark будет использовать компонент Python Anaconda, расположенный в `/usr/bin/anaconda/bin`. Укажите его в настраиваемых действиях с помощью `/usr/bin/anaconda/bin/pip` и `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Открытие Jupyter Notebook PySpark

    ![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-python-package-installation/hdispark.note.jupyter.createpysparknotebook.png "Create a new Jupyter notebook")

4. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

    ![Указание имени для записной книжки](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Provide a name for the notebook")

5. Теперь будет выполнена команда `import tensorflow` и запущен пример hello world. 

    Следует скопировать приведенный ниже код.

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Результат должен выглядеть так:
    
    ![Выполнение кода TensorFlow](./media/hdinsight-apache-spark-python-package-installation/execution.png "Execute TensorFlow code")



## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование внешних пакетов с записными книжками Jupyter в кластерах Apache Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)



<!--HONumber=Dec16_HO1-->


