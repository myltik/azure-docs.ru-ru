---
title: "Устранение неполадок кластера Apache Spark в Azure HDInsight | Документы Майкрософт"
description: "Сведения о проблемах, связанных с кластерами Apache Spark в Azure HDInsight, и их решении."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 079577c73c7067f04c3d9aaa3b2d60ceb5ba9816
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Известные проблемы в работе кластера Apache Spark в HDInsight

В этом документе отслеживаются все известные проблемы с общедоступной предварительной версией Spark HDInsight.  

## <a name="livy-leaks-interactive-session"></a>Утечка интерактивного сеанса Livy
При перезапуске Livy во время работы интерактивного сеанса (из Ambari или в связи с перезагрузкой виртуальной машины headnode 0) произойдет утечка сеанса интерактивного задания. Из-за этого новые задания застревают в состоянии "Принято" и не могут быть запущены.

**Устранение.**

Для решения этой проблемы выполните указанные ниже действия.

1. Подключитесь к головному узлу по протоколу SSH. См. дополнительные сведения об [использовании SSH в HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

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

## <a name="spark-phoenix-connector-is-not-supported"></a>Соединитель Spark-Phoenix не поддерживается

В настоящее время в кластере HDInsight Spark не поддерживается соединитель Spark-Phoenix.

**Устранение.**

Вместо него необходимо использовать соединитель Spark-HBase. Инструкции см. в записи блога [HDinsight – How to use Spark-HBase connector?](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/) (HDinsight: как использовать соединитель Spark-HBase?)

## <a name="issues-related-to-jupyter-notebooks"></a>Проблемы, связанные с записными книжками Jupyter
Ниже приведены некоторые известные проблемы, связанные с записными книжками Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Записные книжки со знаками не из набора ASCII в именах файлов
Записные книжки Jupyter, которые могут использоваться в кластерах Spark HDInsight, не должны содержать в именах файлов знаки не из набора ASCII. При попытке передать через пользовательский интерфейс Jupyter файл, имя которого содержит знаки не из набора ASCII, операция просто не будет выполнена без оповещения пользователя (то есть Jupyter не позволит передать файл и не сообщит об ошибке). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Ошибка при загрузке записных книжек большого размера
При загрузке записных книжек большого размера может возникнуть ошибка **`Error loading notebook`** .  

**Устранение.**

Появление этой ошибки не означает, что данные повреждены или утрачены.  Записные книжки по-прежнему хранятся на диске в каталоге `/var/lib/jupyter`, и вы можете получить к ним доступ, подключившись к кластеру по протоколу SSH. См. дополнительные сведения об [использовании SSH в HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

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
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)

