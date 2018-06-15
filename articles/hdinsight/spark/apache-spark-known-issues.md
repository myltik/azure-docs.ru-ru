---
title: Устранение неполадок кластера Apache Spark в Azure HDInsight | Документы Майкрософт
description: Сведения о проблемах, связанных с кластерами Apache Spark в Azure HDInsight, и их решении.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 664c97117de793209007843fa23c98f52c2b079d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31519245"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Известные проблемы в работе кластера Apache Spark в HDInsight

В этом документе отслеживаются все известные проблемы с общедоступной предварительной версией Spark HDInsight.  

## <a name="livy-leaks-interactive-session"></a>Утечка интерактивного сеанса Livy
При перезапуске Livy (из Ambari или в связи с перезапуском виртуальной машины headnode 0) во время работы интерактивного сеанса происходит утечка сеанса интерактивного задания. В результате этого новые задания могут "зависнуть" в состоянии "Принято".

**Устранение.**

Для решения этой проблемы выполните указанные ниже действия:

1. Подключитесь к головному узлу по протоколу SSH. См. дополнительные сведения об [использовании SSH в HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Выполните следующую команду, чтобы найти идентификаторы приложений для интерактивных заданий, запущенных из Livy: 
   
        yarn application –list
   
    Если задания запущены с помощью интерактивного сеанса Livy без явно заданных имен, по умолчанию используются имена Livy. Для сеанса Livy, запущенного записной книжкой Jupyter, имя задания начинается с remotesparkmagics_ *. 
3. Чтобы аннулировать эти задания, выполните следующую команду: 
   
        yarn application –kill <Application ID>

После этого начинают выполняться новые задания. 

## <a name="spark-history-server-not-started"></a>Сервер журналов Spark не запускается
Сервер журналов Spark не запускается автоматически после создания кластера.  

**Устранение.** 

Вручную запустите сервер журналов из Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Проблема разрешений в каталоге журналов Spark
Отправляя задание с помощью команды spark-submit, hdiuser возвращает следующую ошибку:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
При этом журнал драйверов не ведется. 

**Устранение.**

1. Добавьте hdiuser в группу Hadoop. 
2. После создания кластера предоставьте разрешения 777 для каталога /var/log/spark. 
3. В Ambari измените путь размещения журнала Spark на каталог с разрешениями 777.  
4. Выполните команду spark-submit как sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Соединитель Spark-Phoenix не поддерживается

Кластеры HDInsight Spark не поддерживают соединитель Spark-Phoenix.

**Устранение.**

Вместо него необходимо использовать соединитель Spark-HBase. Инструкции см. в записи блога об [использовании соединителя Spark-HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Проблемы, связанные с записными книжками Jupyter
Ниже приведены некоторые известные проблемы, связанные с записными книжками Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Записные книжки со знаками не из набора ASCII в именах файлов
Не используйте в именах файлов записных книжек Jupyter символы не из набора ASCII. При попытке отправить через пользовательский интерфейс Jupyter файл, имя которого содержит символы не из набора ASCII, происходит сбой без сообщения об ошибке. Jupyter не позволяет отправить файл, но и не выдает явную ошибку.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Ошибка при загрузке записных книжек большого размера
При загрузке записных книжек большого размера может возникнуть ошибка **`Error loading notebook`** .  

**Устранение.**

Появление этой ошибки не означает, что данные повреждены или утрачены.  Записные книжки по-прежнему хранятся на диске в каталоге `/var/lib/jupyter`, и вы можете получить к ним доступ, подключившись к кластеру по протоколу SSH. См. дополнительные сведения об [использовании SSH в HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

После подключения к кластеру с помощью SSH во избежание потери важных данных записные книжки можно скопировать из кластера на локальный компьютер (с помощью SCP или WinSCP), тем самым создав резервную копию. Затем вы сможете использовать туннель SSH к головному узлу через порт 8001, чтобы получить доступ к Jupyter без прохождения через шлюз.  В Jupyter можно очистить выходные данные записной книжки и повторно сохранить ее, чтобы уменьшить ее размер.

Чтобы избежать этой ошибки в будущем, необходимо следовать некоторым рекомендациям.

* Записная книжка должна быть небольшого размера. В записной книжке сохраняются любые выходные данные заданий Spark, которые отправляются обратно в Jupyter.  Не рекомендуем выполнять команду `.collect()` для устойчивых распределенных наборов данных или кадров данных большого размера в Jupyter. Вместо этого, чтобы просмотреть содержимое устойчивого распределенного набора данных, выполните команду `.take()` или `.sample()`. В этом случае объем выходных данных не будет слишком большим.
* Кроме того, если при сохранении записной книжки очистить все ячейки выходных данных, это также поможет уменьшить размер.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Начальная загрузка записной книжки загружается дольше ожидаемого
Выполнение первой инструкции кода в записной книжке Jupyter с использованием волшебного слова Spark может занимать больше минуты.  

**Пояснение**

Это происходит из-за выполнения первой ячейки кода. В фоновом режиме инициируется конфигурация сеанса и задается контекст Spark, SQL и Hive. После настройки этих контекстов выполняется первая инструкция, и из-за этого кажется, что она выполняется долго.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Приостановка записной книжки Jupyter при создании сеанса
Если кластеру Spark не хватает ресурсов, при попытке создать сеанс работа ядер Spark и Pyspark в записной книжке Jupyter приостанавливается. 

**Устранение** 

1. Освободите часть ресурсов в кластере Spark.
   
   * Остановите другие записные книжки Spark в меню "Закрыть и остановить" или нажмите кнопку "Завершить" в обозревателе записных книжек.
   * Остановите другие приложения Spark из YARN.
2. Перезапустите записную книжку, которую вы пытались запустить. Теперь ресурсов должно быть достаточно для создания сеанса.

## <a name="see-also"></a>См. также
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
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

