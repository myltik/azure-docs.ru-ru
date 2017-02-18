---
title: "Известные проблемы в работе кластера Apache Spark в Azure HDInsight | Документация Майкрософт"
description: "Известные проблемы в работе кластеров Apache Spark в Azure HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 6c81d978e470754f5c0a737aba0437e105949099


---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Известные проблемы в работе кластера Apache Spark в HDInsight

В этом документе отслеживаются все известные проблемы с общедоступной предварительной версией Spark HDInsight.  

## <a name="livy-leaks-interactive-session"></a>Утечка интерактивного сеанса Livy
При перезапуске Livy во время работы интерактивного сеанса (из Ambari или в связи с перезагрузкой виртуальной машины headnode 0) происходит утечка сеанса интерактивного задания. Из-за этого новые задания застревают в состоянии "Принято" и не могут быть запущены.

**Устранение.**

Для решения этой проблемы выполните указанные ниже действия.

1. Подключитесь к головному узлу по протоколу SSH. Дополнительные сведения для клиентов Windows см. в статье [Использование SSH с HDInsight (Hadoop) в PuTTY на базе Windows](hdinsight-hadoop-linux-use-ssh-windows.md), а для клиентов Linux, Unix или OS X — в статье [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md). 
2. Выполните следующую команду, чтобы найти идентификаторы приложений для интерактивных заданий, запущенных из Livy: 
   
        yarn application –list
   
    По умолчанию задания, запущенные в интерактивном сеансе Livy без прямо заданных имен, будут иметь имя Livy. Если сеанс Livy запускается из записной книжки Jupyter, имя задания будет начинаться с remotesparkmagics_*. 
3. Чтобы аннулировать эти задания, выполните следующую команду: 
   
        yarn application –kill <Application ID>

После этого новые задания начнут выполняться. 

## <a name="spark-history-server-not-started"></a>Сервер журналов Spark не запускается
Сервер журналов Spark не запускается автоматически после создания кластера.  

**Устранение.** 

Вручную запустите сервер журналов из Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Проблема разрешений в каталоге журналов Spark
Когда hdiuser отправляет задание с параметром spark-submit, возникает ошибка java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Отказано в доступе), а журнал драйверов не перезаписывается. 

**Устранение.**

1. Добавьте hdiuser в группу Hadoop. 
2. После создания кластера предоставьте разрешения 777 для каталога /var/log/spark. 
3. В Ambari измените путь размещения журнала Spark на каталог с разрешениями 777.  
4. Выполните команду spark-submit как sudo.  

## <a name="issues-related-to-jupyter-notebooks"></a>Проблемы, связанные с записными книжками Jupyter
Ниже приведены некоторые известные проблемы, связанные с записными книжками Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Записные книжки со знаками не из набора ASCII в именах файлов
Записные книжки Jupyter, которые могут использоваться в кластерах Spark HDInsight, не должны содержать в именах файлов знаки не из набора ASCII. При попытке передать через пользовательский интерфейс Jupyter файл, имя которого содержит знаки не из набора ASCII, операция просто не будет выполнена без оповещения пользователя (то есть Jupyter не позволит передать файл и не сообщит об ошибке). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Ошибка при загрузке записных книжек большого размера
При загрузке записных книжек большого размера может возникнуть ошибка **`Error loading notebook`** .  

**Устранение.**

Появление этой ошибки не означает, что данные повреждены или утрачены.  Записные книжки по-прежнему хранятся на диске в каталоге `/var/lib/jupyter`, и вы можете получить к ним доступ, подключившись к кластеру по протоколу SSH. Дополнительные сведения для клиентов Windows см. в статье [Использование SSH с HDInsight (Hadoop) в PuTTY на базе Windows](hdinsight-hadoop-linux-use-ssh-windows.md), а для клиентов Linux, Unix или OS X — в статье [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

После подключения к кластеру с помощью SSH во избежание потери важных данных записные книжки можно скопировать из кластера на локальный компьютер (с помощью SCP или WinSCP), тем самым создав резервную копию. Затем вы сможете использовать туннель SSH к головному узлу через порт 8001, чтобы получить доступ к Jupyter без прохождения через шлюз.  В Jupyter можно очистить выходные данные записной книжки и повторно сохранить их, чтобы уменьшить ее размер.

Чтобы избежать этой ошибки в будущем, необходимо следовать некоторым рекомендациям.

* Записная книжка должна быть небольшого размера. В записной книжке сохраняются любые выходные данные заданий Spark, которые отправляются обратно в Jupyter.  Рекомендуется не выполнять команду `.collect()` для устойчивых распределенных наборов данных или кадров данных большого размера в Jupyter. Вместо этого, чтобы просмотреть содержимое устойчивого распределенного набора данных, выполните команду `.take()` или `.sample()`. В этом случае выходные данные не будут слишком большими.
* Кроме того, если при сохранении записной книжки очистить все ячейки выходных данных, это также поможет уменьшить размер.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Начальная загрузка записной книжки загружается дольше ожидаемого
Выполнение первой инструкции кода в записной книжке Jupyter с использованием волшебного слова Spark может занимать больше минуты.  

**Пояснение**

Это происходит из-за выполнения первой ячейки кода. В фоновом режиме инициируется конфигурация сеанса и задается контекст Spark, SQL и Hive. После настройки этих контекстов выполняется первая инструкция, и из-за этого кажется, что она выполняется долго.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Приостановка записной книжки Jupyter при создании сеанса
Если кластеру Spark не хватает ресурсов, при попытке создания сеанса ядра Spark и Pyspark в записной книжке Jupyter будут приостановлены. 

**Устранение** 

1. Освободите часть ресурсов в кластере Spark.
   
   * Остановите другие записные книжки Spark в меню "Закрыть и остановить" или нажмите кнопку "Завершить" в обозревателе записных книжек.
   * Остановите другие приложения Spark из YARN.
2. Перезапустите записную книжку, которую вы пытались запустить. Теперь ресурсов должно быть достаточно для создания сеанса.

## <a name="see-also"></a>Дополнительные материалы
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
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)




<!--HONumber=Jan17_HO4-->


