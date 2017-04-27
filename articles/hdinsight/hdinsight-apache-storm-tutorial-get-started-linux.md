---
title: "Начало работы с кластером Apache Storm в Azure HDInsight | Документация Майкрософт"
description: "Начало работы с аналитикой больших данных с помощью Apache Storm и примеров Storm Starter под управлением Linux в HDInsight. Информация об использовании Storm для обработки данных в режиме реального времени."
keywords: "apache storm, учебник sapache storm, анализ больших данных, storm starter"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/17/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 4787928ed066b9aed51a8512deeda6cd49897d82
ms.lasthandoff: 04/11/2017

---
#<a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-linux-based-hdinsight"></a>Начало работы с примерами Storm Starter для аналитики больших данных в HDInsight под управлением Linux

Apache Storm — это масштабируемая отказоустойчивая распределенная система выполнения расчетов для обработки данных потоковой передачи в режиме реального времени. С помощью Storm вы можете создать в Azure HDInsight облачный кластер, который анализирует данные в режиме реального времени.

> [!IMPORTANT]
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Знакомство с SSH и SCP**. См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="access-control-requirements"></a>Требования к контролю доступа

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Создание кластера Storm

Чтобы создать Storm в кластере HDInsight, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com) последовательно выберите элементы **+Создать**, **Аналитика** и **HDInsight**.

    ![Создание кластера HDInsight](./media/hdinsight-apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. В колонке **Основные сведения** задайте следующие параметры.

    * **Имя кластера.** Имя кластера HDInsight.
    * **Подписка.** Выберите нужную подписку.
    * **Имя пользователя для входа в кластер** и **Пароль для входа в кластер**. Имя для входа в кластер по протоколу HTTPS. Эти учетные данные используются для доступа к таким службам, как веб-интерфейс Ambari или REST API.
    * **Secure Shell (SSH) username** (Имя пользователя Secure Shell (SSH)). Имя для доступа к кластеру по протоколу SSH. По умолчанию пароль совпадает с паролем для входа в кластер.
    * **Группа ресурсов.** Группа ресурсов, в которой будет создан кластер.
    * **Расположение.** Регион Azure, в котором будет создан кластер.

    ![Выберите подписку.](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. Выберите **тип кластера** и в колонке **Конфигурация кластера** задайте следующие значения:

    * **Тип кластера**: Storm.

    * **Операционная система**: Linux.

    * **Версия**: Storm 1.0.1 (HDI 3.5).

    * **Ценовая категория кластера**: "Стандартный".

    Затем нажмите кнопку **Выбрать**, чтобы сохранить эти параметры.

    ![Выбор типа кластера](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. Выбрав тип кластера, нажмите кнопку __Выбрать__, чтобы установить выбранный тип. Затем нажмите кнопку __Далее__, чтобы завершить настройку основных параметров.

5. В колонке **Хранилище** выберите или создайте учетную запись хранения. Для действий, описанных в этом документе, в других полях этой колонки оставьте значения по умолчанию. Нажмите кнопку __Далее__, чтобы сохранить конфигурацию хранилища.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. В колонке **Сводка** просмотрите конфигурацию для кластера. Используйте ссылки __Изменить__, чтобы изменить неправильные параметры. Наконец, нажмите кнопку "Создать" для создания кластера.

    ![Сводка по конфигурации кластера](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)

    > [!NOTE]
    > Операция создания кластера может занять до 20 минут.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Запуск образца Storm Starter в HDInsight

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Если для защиты учетной записи SSH используется пароль, то предлагается ввести его. Если используется открытый ключ, то может потребоваться использовать параметр `-i`, чтобы указать соответствующий закрытый ключ. Например, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

    См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Запустите пример топологии, используя следующую команду:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount

    > [!NOTE]
    > В предыдущих версиях HDInsight использовалось имя класса топологии `storm.starter.WordCountTopology` вместо `org.apache.storm.starter.WordCountTopology`.

    Эта команда запускает в кластере пример топологии WordCount с понятным именем wordcount. Он случайным образом формирует предложения и подсчитывает количество экземпляров каждого слова в предложениях.

    > [!NOTE]
    > При отправке пользовательской топологии в кластер необходимо сначала скопировать JAR-файл, содержащий кластер, а затем воспользоваться командой `storm`. Чтобы скопировать файл, воспользуйтесь командой `scp`. Например, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > Пример WordCount и другие начальные примеры использования Storm уже включены в ваш кластер в `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Если вам интересно изучить исходный код для примеров Storm начального уровня, вы можете найти их здесь: [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter). Это ссылка для версии Storm 1.0.x, которая поставляется вместе с HDInsight 3.5. Для других версий Storm нажмите кнопку __Ветвь__ в верхней части страницы и выберите нужную версию.

## <a name="monitor-the-topology"></a>Мониторинг топологии

Пользовательский интерфейс Storm предоставляет веб-интерфейс для работы с запущенными топологиями и включен в состав кластера HDInsight.

Чтобы начать мониторинг топологии из пользовательского интерфейса Storm, выполните следующие действия.

1. Чтобы отобразить пользовательский интерфейс Storm, откройте в веб-браузере страницу с адресом https://CLUSTERNAME.azurehdinsight.net/stormui. Замените **CLUSTERNAME** именем кластера.

    > [!NOTE]
    > При появлении соответствующего запроса введите имя пользователя и пароль администратора кластера (admin), которые использовались при создании кластера.

2. В разделе**Topology summary** (Сводка по топологии) выберите запись **wordcount** (количество слов) в столбце **Name** (Имя). Отобразятся сведения о топологии.

    ![Панель мониторинга Storm, отображающая сведения о топологии WordCount в Storm Starter.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

    Эта страница содержит следующую информацию.

    * **Topology stats** (Статистика топологий) —основная информация о производительности топологии, упорядоченная по временным промежуткам.

        > [!NOTE]
        > При выборе определенного временного промежутка меняется информация, отображаемая в других разделах страницы.

    * **Spouts** (Воронки) — основная информация о воронках, в том числе последняя ошибка, возвращенная каждой воронкой.

    * **Bolts** (Сита) — основная информация о ситах.

    * **Topology configuration** (Конфигурация топологии) — подробная информация о конфигурации топологии.

    Эта страница также содержит действия, которые можно выполнять в топологии.

    * **Activate** (Включить) — возобновление обработки отключенной топологии.

    * **Deactivate** (Отключить) — приостановка выполняемой топологии.

    * **Rebalance**(Повторная балансировка) — корректировка параллелизма топологии. После изменения числа узлов в кластере необходимо выполнить повторную балансировку топологий. Повторная балансировка корректирует параллелизм для компенсации увеличения или уменьшения количества узлов в кластере. Дополнительные сведения см. в статье [Understanding the parallelism of a Storm topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) (Общие сведения о параллелизме топологии Storm).

    * **Kill** (Удалить) — останавливает выполнение топологии Storm по истечении заданного времени ожидания.

3. На этой странице выберите запись и раздела **Spouts** (Воронки) или **Bolts** (Сита). Отобразятся сведения о выбранном компоненте.

    ![Панель мониторинга Storm со сведениями о выбранных компонентах.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

    На этой странице отображается следующая информация.

    * **Spout/Bolt stats** (Статистика воронки/сита) —основная информация о производительности соответствующего компонента, упорядоченная по временным промежуткам.

        > [!NOTE]
        > При выборе определенного временного промежутка меняется информация, отображаемая в других разделах страницы.

    * **Input stats** (Статистика ввода, только для сита) — информация о компонентах, которые производят данные, используемые ситом.

    * **Output stats** (Статистика вывода) — информация о данных, созданных этим ситом.

    * **Executors** (Исполнители) — информация об экземплярах этого компонента.

    * **Errors** (Ошибки) — ошибки, созданные этим компонентом.

4. При просмотре информации о воронке и сите выберите запись из столбца **Port** (Порт) в разделе **Executors** (Исполнители), чтобы просмотреть информацию о конкретном экземпляре компонента.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    В этом примере слово **seven** (семь) использовалось 1 493 957 раз. Столько раз это слово было обнаружено с момента запуска данной топологии.

## <a name="stop-the-topology"></a>Остановка топологии

Вернитесь к странице **Topology summary** (Сводка по топологии), чтобы найти топологию подсчета слов, и нажмите кнопку **Kill** (Удалить) в разделе **Topology actions** (Действия топологии). При появлении запроса введите 10 секунд ожидания перед остановкой топологии. По истечении времени ожидания топология перестанет отображаться при переходе к разделу **Пользовательский интерфейс Storm** на панели мониторинга.

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a id="next"></a>Дальнейшие действия

В этом руководстве по Apache Storm описаны основные принципы работы со Storm в HDInsight. Затем вы можете научиться [разрабатывать топологии на платформе Java с помощью Maven](hdinsight-storm-develop-java-topology.md).

Если вы уже знакомы с разработкой топологий на основе Java и хотите развернуть существующую топологию в HDInsight, см. статью [Развертывание топологий Apache Storm в HDInsight под управлением Linux и управление ими](hdinsight-storm-deploy-monitor-topology-linux.md).

Если вы разработчик .NET, вы можете создать топологию C# или гибридную топологию C# или Java с помощью Visual Studio. Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Например топологии, которые можно использовать при помощи Storm в HDInsight, см. в следующих примерах:

* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[preview-portal]: https://portal.azure.com/

