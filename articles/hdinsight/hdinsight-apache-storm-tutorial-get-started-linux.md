---
title: "Руководство по Apache Storm: начало работы со Storm под управлением Linux в HDInsight | Документация Майкрософт"
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
ms.date: 10/12/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 84567b646e9cb5e95b2aaf5c3ce19fc227decc82


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Учебник по Apache Storm в HDInsight: начало работы с анализом больших объемов данных в HDInsight с помощью примеров Storm Starter
Apache Storm — это масштабируемая отказоустойчивая распределенная система выполнения расчетов для обработки данных потоковой передачи в режиме реального времени. С помощью Storm вы можете создать в Azure HDInsight облачный кластер, который анализирует данные в режиме реального времени.

> [!NOTE]
> С помощью действий, описанных в данной статье, можно создать кластер HDInsight под управлением Linux. Инструкции по созданию Storm под управлением Windows в кластере HDInsight см. в статье [Учебник по Apache Storm в HDInsight: начало работы с анализом больших объемов данных в HDInsight с помощью примеров Storm Starter](hdinsight-apache-storm-tutorial-get-started.md).
> 
> 

## <a name="prerequisites"></a>Предварительные требования
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Необходимое условие для успешного выполнения инструкций этого учебника:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Знакомство с SSH и SCP**. Дополнительные сведения об использовании SSH и SCP с HDInsight можно найти в следующих разделах:
  
  * **Для клиентов Linux, Unix или OS X**: ознакомьтесь с разделом [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
  * **Для клиентов Windows**: ознакомьтесь с разделом [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Создание кластера Storm
В этом разделе мы создадим кластер HDInsight версии 3.2 (Storm версии 0.9.3) с помощью шаблона Azure Resource Manager. Дополнительную информацию о версиях HDInsight и их соглашениях об уровне обслуживания см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight](hdinsight-component-versioning.md). Другие способы создания кластера см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Щелкните следующее изображение, чтобы открыть шаблон на портале Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Шаблон находится в общедоступном контейнере BLOB-объектов: *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*. 
2. В колонке "Параметры" заполните следующие поля:
   
   * **Имя кластера**: введите имя создаваемого кластера Hadoop.
   * **Имя для входа в кластер и пароль**: имя для входа по умолчанию — admin.
   * **Имя пользователя SSH и пароль**.
     
     Запишите эти значения.  Они потребуются позже в данном руководстве.
     
     > [!NOTE]
     > SSH используется для удаленного доступа к кластеру HDInsight с помощью командной строки. Эти имя пользователя и пароль будут использоваться при подключении к кластеру через SSH. Имя пользователя SSH должно быть уникальным, так как на его основе создается учетная запись пользователя на всех узлах кластера HDInsight. Вот некоторые имена учетных записей, которые зарезервированы для служб в кластере и не должны использоваться в качестве имени пользователя SSH:
     > 
     > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.
     > 
     > Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.
     > 
     > * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
     > 
     > 

3. Нажмите кнопку **ОК**, чтобы сохранить параметры.

4. В колонке **Настраиваемое развертывание** щелкните раскрывающийся список **Группа ресурсов** и выберите пункт **Создать**, чтобы создать группу ресурсов. Группа ресурсов — это контейнер, в который входит кластер, зависимая учетная запись хранения и другие связанные ресурсы.

5. Щелкните **Юридические условия** и **Создать**.

6. Нажмите кнопку **Создать**. Вы увидите новый элемент под названием "Идет отправка развертывания для развертывания шаблона". Процесс создания кластера и базы данных SQL занимает около 20 минут.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Запуск образца Storm Starter в HDInsight
Примеры [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) включены в кластер HDInsight. Выполняя следующие действия, вы запустите пример WordCount.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Если для защиты учетной записи SSH используется пароль, будет предложено ввести его. Если используется открытый ключ, может потребоваться использовать параметр `-i` , чтобы указать соответствующий закрытый ключ. Например, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Дополнительная информация об использовании SSH с HDInsight на основе Linux приведена в следующих статьях:
   
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. Запустите пример топологии, используя следующую команду:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm.starter.WordCountTopology wordcount
   
   > [!NOTE]
   > Часть имени файла `*` соответствует номеру версии, который изменяется при обновлении HDInsight.
   > 
   > 
   
    Эта команда запустит в кластере пример топологии WordCount с понятным именем wordcount. Он будет случайным образом формировать предложения и подсчитывать количество экземпляров каждого слова в предложениях.
   
   > [!NOTE]
   > При отправке топологии в кластер необходимо сначала скопировать jar-файл, содержащий кластер, а затем воспользоваться командой `storm`. Это можно сделать, выполнив команду `scp` на клиенте, на котором находится файл. Например, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   > 
   > Пример WordCount и другие начальные примеры использования Storm уже включены в ваш кластер в `/usr/hdp/current/storm-client/contrib/storm-starter/`.
   > 
   > 

## <a name="monitor-the-topology"></a>Мониторинг топологии
Пользовательский интерфейс Storm предоставляет веб-интерфейс для работы с запущенными топологиями и включен в состав кластера HDInsight.

Чтобы начать мониторинг топологии из пользовательского интерфейса Storm, выполните следующие действия.

1. Откройте в веб-браузере страницу с адресом https://CLUSTERNAME.azurehdinsight.net/stormui, в котором **CLUSTERNAME** — имя вашего кластера. Откроется пользовательский интерфейс Storm.
   
   > [!NOTE]
   > При появлении соответствующего запроса введите имя пользователя и пароль администратора кластера (admin), которые использовались при создании кластера.
   > 
   > 
2. В разделе**Topology summary** (Сводка по топологии) выберите запись **wordcount** (количество слов) в столбце **Name** (Имя). После этого отобразится дополнительная информация о топологии.
   
    ![Панель мониторинга Storm, отображающая сведения о топологии WordCount в Storm Starter.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
   
    Эта страница содержит следующую информацию.
   
   * **Topology stats** (Статистика топологий) —основная информация о производительности топологии, упорядоченная по временным промежуткам.
     
     > [!NOTE]
     > При выборе определенного временного промежутка меняется информация, отображаемая в других разделах страницы.
     > 
     > 
   * **Spouts** (Воронки) — основная информация о воронках, в том числе последняя ошибка, возвращенная каждой воронкой.
   * **Bolts** (Сита) — основная информация о ситах.
   * **Topology configuration** (Конфигурация топологии) — подробная информация о конфигурации топологии.
     
     Эта страница также содержит действия, которые можно выполнять в топологии.
   * **Activate** (Включить) — возобновление обработки отключенной топологии.
   * **Deactivate** (Отключить) — приостановка выполняемой топологии.
   * **Rebalance**(Повторная балансировка) — корректировка параллелизма топологии. После изменения числа узлов в кластере необходимо выполнить повторную балансировку топологий. Это позволяет топологии скорректировать параллелизм для компенсации увеличения или уменьшения количества узлов в кластере. Дополнительные сведения см. в статье [Understanding the parallelism of a Storm topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) (Общие сведения о параллелизме топологии Storm).
   * **Kill** (Удалить) — останавливает выполнение топологии Storm по истечении заданного времени ожидания.
3. На этой странице выберите запись и раздела **Spouts** (Воронки) или **Bolts** (Сита). Отобразится информация о выбранном компоненте.
   
    ![Панель мониторинга Storm со сведениями о выбранных компонентах.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    На этой странице отображается следующая информация.
   
   * **Spout/Bolt stats** (Статистика воронки/сита) —основная информация о производительности соответствующего компонента, упорядоченная по временным промежуткам.
     
     > [!NOTE]
     > При выборе определенного временного промежутка меняется информация, отображаемая в других разделах страницы.
     > 
     > 
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
   
    Эти данные показывают, что слово **seven** (семь) использовалось 1 493 957 раз. Столько раз оно было обнаружено с момента запуска данной топологии.

## <a name="stop-the-topology"></a>Остановка топологии
Вернитесь к странице **Topology summary** (Сводка по топологии), чтобы найти топологию подсчета слов, и нажмите кнопку **Kill** (Удалить) в разделе **Topology actions** (Действия топологии). При появлении запроса введите 10 секунд ожидания перед остановкой топологии. После истечения времени ожидания топология перестанет отображаться во время входа в раздел **Storm UI** (Пользовательский интерфейс Storm) панели мониторинга.

## <a name="delete-the-cluster"></a>Удаление кластера
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="a-idnextanext-steps"></a><a id="next"></a>Дальнейшие действия
В этом учебнике Apache Storm используется Storm Starter для объяснения принципов создания Storm в кластере HDInsight и использования панели мониторинга Storm для развертывания и отслеживания топологий Storm, а также управления ими. Затем вы можете научиться [разрабатывать топологии на платформе Java с помощью Maven](hdinsight-storm-develop-java-topology.md).

Если вы уже знакомы с разработкой топологий на основе Java и хотите развернуть существующую топологию в HDInsight, см. статью [Развертывание топологий Apache Storm в HDInsight под управлением Linux и управление ими](hdinsight-storm-deploy-monitor-topology-linux.md).

Если вы разработчик .NET, вы можете создать топологию C# или гибридную топологию C# или Java с помощью Visual Studio. Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Например топологии, которые можно использовать при помощи Storm в HDInsight, см. в следующих примерах:

    * [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Nov16_HO2-->


