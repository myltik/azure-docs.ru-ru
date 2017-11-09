---
title: "Анализ полученных с датчиков данных с помощью Apache Storm и HBase | Документация Майкрософт"
description: "В этой статье вы узнаете, как подключить к Apache Storm к виртуальной сети. Используйте Storm с HBase для обработки данных датчиков из концентратора событий и их визуализации с помощью D3.js."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/19/2017
ms.author: larryfr
ms.openlocfilehash: 87c2aece68c5de06d683abf971b6c7ccf7f67a54
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Анализ полученных с датчиков данных с использованием Apache Storm, концентратора событий и базы данных HBase в службе HDInsight (Hadoop)

Узнайте, как с помощью Apache Storm в HDInsight можно обрабатывать данные датчиков из концентратора событий Azure. Эти данные можно сохранить в Apache HBase в HDInsight и визуализировать с помощью D3.js.

С помощью шаблона Azure Resource Manager, используемого в этом документе, показывается, как создать несколько ресурсов Azure в группе ресурсов. Этот шаблон создает виртуальную сеть Azure, два кластера HDInsight (Storm и HBase) и веб-приложение Azure. Реализация node.js панели мониторинга в реальном времени автоматически развертывается в веб-приложении.

> [!NOTE]
> Для работы с этим документом требуется HDInsight версии 3.6.
>
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="architecture"></a>Архитектура

![схема архитектуры](./media/apache-storm-sensor-data-analysis/devicesarchitecture.png)

В этом примере используются следующие компоненты:

* **Концентраторы событий Azure**: содержат собираемые с датчиков данные.
* **Storm в HDInsight**: в реальном времени обрабатывает данные, получаемые из концентратора событий.
* **HBase в HDInsight.**Обеспечивает постоянное хранилище данных NoSQL для данных, обработанных Storm.
* **Служба виртуальной сети Azure**: обеспечивает безопасный обмен данными между кластерами Storm в HDInsight и HBase в HDInsight.
  
  > [!NOTE]
  > Виртуальная сеть требуется при использовании клиентского API Java для HBase. Она не предоставляется через общедоступный шлюз для кластеров HBase. Установка кластеров HBase и Storm в одной виртуальной сети позволяет кластеру Storm (или любой другой системе в виртуальной сети) обращаться непосредственно к кластеру HBase с помощью клиентского API.

* **Веб-сайт панели мониторинга**: пример панели мониторинга, на которой в режиме реального времени строятся диаграммы на основе полученных данных.
  
  * Веб-сайт реализован в Node.js.
  * [Socket.io](http://socket.io/) .
    
    > [!NOTE]
    > Использование Socket.io для связи относится к тонкостям реализации. На практике можно использовать любую платформу, например сокеты прямого доступа WebSocket или SignalR.

  * [D3.js](http://d3js.org/) .

> [!IMPORTANT]
> Требуются два кластера, так как метода для создания одного кластера HDInsight для Storm и для HBase не существует.

Топология считывает данные из концентратора событий с помощью класса `org.apache.storm.eventhubs.spout.EventHubSpout` и записывает данные в HBase с помощью класса `org.apache.storm.hbase.bolt.HBaseBolt`. Связь с веб-сайтом реализована с использованием библиотеки [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

На следующей схеме поясняется структура топологии.

![схема топологии](./media/apache-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Это упрощенное представление топологии. Для каждого раздела концентратора событий создается экземпляр каждого компонента. Экземпляры распределяются между узлами в кластере. Обмен данными между ними выглядит следующим образом.
> 
> * Данные из «воронки» равномерно передаются в средство синтаксического анализа.
> * Данные из средства синтаксического анализа передаются в панель мониторинга и базу данных HBase. Данные группируются по идентификатору устройства, чтобы сообщения с одного устройства всегда перенаправлялись в один и тот же компонент.

### <a name="topology-components"></a>Компоненты топологии

* **Компонент концентратора событий spout**: входит в состав Apache Storm версии 0.10.0 и выше.
  
  > [!NOTE]
  > Для работы этого компонента, используемого в нашем примере, требуется кластер Storm в HDInsight версии 3.5 или 3.6.

* **ParserBolt.java**: из воронки отправляются необработанные данные JSON. Иногда за один раз может отправляться сразу несколько событий. Этот компонент bolt считывает данные, генерируемые компонентом spout, и выполняет синтаксический анализ сообщения JSON. Затем компонент bolt передает данные как кортеж, содержащий несколько полей.
* **DashboardBolt.java**: этот компонент показывает, как использовать клиентскую библиотеку Socket.io для Java для отправки данных в реальном времени в веб-панель мониторинга.
* **no-hbase.yaml**: определение топологии, используемое во время выполнения в локальном режиме. Компоненты HBase при этом не используются.
* **with-hbase.yaml**: определение топологии, используемое во время выполнения топологии в кластере. Компоненты HBase при этом используются.
* **dev.properties**: сведения о конфигурации для компонента spout концентратора событий, компонента bolt HBase и компонентов панели мониторинга.

## <a name="prepare-your-environment"></a>Подготовка среды

Прежде чем использовать этот пример, необходимо подготовить среду разработки. Также необходимо создать концентратор событий Azure, который используется в этом примере.

Вам потребуется установить в среде разработки следующие элементы:

* [Node.js](http://nodejs.org/): используется для локального предварительного просмотра веб-панели мониторинга в среде разработки.
* [Java и пакет JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): используется для разработки топологии Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): используется для создания и компиляции проекта.
* [Git](http://git-scm.com/): используется для скачивания проекта с сайта GitHub.
* Клиент **SSH**: используется для подключения к кластерам HDInsight под управлением Linux. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

Выполните инструкции по [созданию концентратора событий](../../event-hubs/event-hubs-create.md).

> [!IMPORTANT]
> Запишите имя, пространство имен и ключ концентратора событий для RootManageSharedAccessKey. Эти сведения используются для настройки топологии Storm.

Вам не нужен кластер HDInsight. Выполнив действия, описанные в этом документе, вы получите шаблон Azure Resource Manager, который создаст ресурсы, необходимые для этого примера. Шаблон создает следующие ресурсы:
 
* виртуальная сеть Azure;
* кластер Storm в HDInsight (под управлением Linux, с двумя рабочими узлами);
* кластер HBase в HDInsight (под управлением Linux, с двумя рабочими узлами);
* веб-приложение Azure, в котором размещается панель мониторинга.

## <a name="download-and-configure-the-project"></a>Скачивание и настройка проекта

Для скачивания проекта с GitHub выполните следующую команду.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

После выполнения команды структура каталогов будет следующей:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> В этом документе не приводятся подробные сведения о коде примера. Тем не менее, код содержит полные комментарии.

Чтобы настроить проект для чтения из концентратора событий, откройте файл `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` и добавьте сведения о концентраторе событий в следующие строки:

```bash
eventhub.policy.key: the_key_for_RootManageSharedAccessKey
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>Локальная компиляция и тестирование

> [!IMPORTANT]
> Для локального использования топологии требуется рабочая среда разработки Storm. См. дополнительные сведения о [настройке среды разработки Storm](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) на сайте Apache.org.

Перед началом тестирования вам необходимо запустить панель мониторинга, чтобы просмотреть выходные данные топологии и создать данные для хранения в концентраторе событий.

> [!IMPORTANT]
> При локальном тестировании компонент HBase этой топологии неактивен. Доступ к API Java для кластера HBase нельзя получить за пределами виртуальной сети Azure, содержащей кластеры.

### <a name="start-the-web-application"></a>Запуск веб-приложения

1. В командной строке измените каталог на `hdinsight-eventhub-example/dashboard`. Чтобы установить зависимости, необходимые для веб-приложения, выполните следующую команду.
   
    ```bash
    npm install
    ```

2. Запустите веб-приложение с помощью следующей команды.
   
    ```bash
    node server.js
    ```
   
    Должно появиться сообщение следующего вида.
   
        Server listening at port 3000

3. Откройте веб-браузер и введите `http://localhost:3000/` как адрес. Откроется страница, аналогичная следующей:
   
    ![веб-панель мониторинга](./media/apache-storm-sensor-data-analysis/emptydashboard.png)
   
    Не закрывайте окно командной строки. После завершения тестирования остановите веб-сервер с помощью клавиш CTRL+C.

### <a name="generate-data"></a>Создание данных

> [!NOTE]
> Для описанных в этом разделе действий используется Node.js, поэтому эти действия могут выполняться на любой платформе. Примеры для других языков см. в каталоге `SendEvents`.

1. Откройте новую строку, оболочку или окно терминала и измените каталог на `hdinsight-eventhub-example/SendEvents/nodejs`. Чтобы установить зависимости, необходимые для веб-приложения, выполните следующую команду.

    ```bash
    npm install
    ```

2. Откройте файл `app.js` в текстовом редакторе и добавьте полученные ранее сведения о концентраторе событий.
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'Your-eventhub-namespace';
    // Event Hub Name
    var hubname ='Your-eventhub-name';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'RootManageSharedAccessKey';
    var my_key = 'Your-Key';
    ```
   
   > [!NOTE]
   > В этом примере предполагается, что вы используете `RootManageSharedAccessKey` для доступа к концентратору событий.

3. Для добавления новых записей в концентратор событий используйте следующую команду.
   
    ```bash
    node app.js
    ```
   
    Вы должны увидеть несколько строк выходных данных, среди которых будет информация, отправленная в концентратор событий.
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="build-and-start-the-topology"></a>Создание и запуск топологии

1. В новой командной строке измените каталог на `hdinsight-eventhub-example/TemperatureMonitor`. Для сборки и создания пакета топологии выполните следующую команду: 

    ```bash
    mvn clean package
    ```

2. Запустите топологию в локальном режиме с помощью следующей команды:

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * Команда `--local` запускает топологию в локальном режиме.
    * Команда `--filter` использует файл `dev.properties` для указания значений параметров в определении топологии.
    * Команда `resources/no-hbase.yaml` использует определение топологии `no-hbase.yaml`.
 
   После запуска топология считывает записи из концентратора событий и отправляет их на панель мониторинга, работающую на локальном компьютере. На веб-панели мониторинга должны появиться линии, как показано на рисунке ниже.
   
    ![панель мониторинга с данными](./media/apache-storm-sensor-data-analysis/datadashboard.png)

2. Не закрывая панель мониторинга, отправьте новые данные в концентраторы событий с помощью команды `node app.js` (использовалась на предыдущих шагах). Так как значения температуры генерируются случайным образом, граф будет изменяться, отображая значительные изменения температуры.
   
   > [!NOTE]
   > Выполнять команду `node app.js` необходимо из каталога **hdinsight-eventhub-example/SendEvents/Nodejs**.

3. Убедившись, что панель мониторинга обновляется, остановите топологию, нажав клавиши CTRL+C. С помощью клавиш Ctrl+C можно также остановить локальный веб-сервер.

## <a name="create-a-storm-and-hbase-cluster"></a>Создание кластеров Storm и HBase

В действиях, описанных в этом разделе, используется [шаблон Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md) для создания виртуальной сети Azure, содержащей кластеры Storm и HBase. Шаблон также создает веб-приложение Azure и развертывает в нем копию панели мониторинга.

> [!NOTE]
> Виртуальная сеть используется для того, чтобы топология, запущенная на кластере Storm, могла непосредственно обмениваться данными с кластером HBase с помощью API Java для HBase.

Шаблон Resource Manager, используемый в этом документе, расположен в общедоступном контейнере BLOB-объектов по адресу **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**.

1. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Resource Manager на портале Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/apache-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. В колонке **Настраиваемое развертывание** укажите следующие значения:
   
    ![Параметры HDInsight](./media/apache-storm-sensor-data-analysis/parameters.png)
   
   * **Base Cluster Name** (Базовое имя кластера). Это значение будет использоваться в качестве базового имени для кластеров Storm и HBase. Например, если ввести **abc**, будут созданы кластер Storm **storm-abc** и кластер HBase **hbase-abc**.
   * **Cluster Login User Name** (Имя пользователя для входа в кластер). Имя администратора для кластеров Storm и HBase.
   * **Cluster Login User Password** (Пароль пользователя для входа в кластер). Имя администратора для кластеров Storm и HBase.
   * **Имя пользователя SSH**. Создаваемый пользователь SSH для кластеров Storm и HBase.
   * **Пароль SSH**. Пароль пользователя SSH для кластеров Storm и HBase.
   * **Расположение.** Регион, в котором создаются кластеры.
     
     Нажмите кнопку **ОК** , чтобы сохранить параметры.

3. В разделе **Основные** создайте новую группу ресурсов или выберите существующую.
4. Из раскрывающегося меню **Расположение группы ресурсов** выберите расположение, указанное в параметре **Расположение** в разделе **Параметры**.
5. Прочтите условия использования и установите флажок **Я принимаю указанные выше условия**.
6. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. Процесс создания кластеров занимает около 20 минут.

Когда ресурсы будут созданы, отобразится колонка со сведениями о группе ресурсов.

![Группа ресурсов для виртуальной сети и кластеров](./media/apache-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **storm-BASENAME** и **hbase-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам. Кроме того, обратите внимание, что имя сайта панели мониторинга — **basename-dashboard**. Это значение используется ниже в этом документе.

## <a name="configure-the-dashboard-bolt"></a>Настройка сита панели мониторинга

Для отправки данных в панель мониторинга, развернутую в виде веб-приложения, необходимо изменить в файле `dev.properties` следующую строку:

```yaml
dashboard.uri: http://localhost:3000
```

Измените `http://localhost:3000` на `http://BASENAME-dashboard.azurewebsites.net` и сохраните файл. Замените **BASENAME** базовым именем, введенным на предыдущем шаге. Можно также использовать созданную ранее группу ресурсов, чтобы выбрать панель мониторинга и просмотреть URL-адрес.

## <a name="create-the-hbase-table"></a>Создание таблицы HBase

Чтобы хранить данные в HBase, необходимо сначала создать таблицу. Заранее создайте ресурсы, в которые Storm будет записывать данные, так как попытка создать ресурсы изнутри топологии Storm может привести к тому, что несколько экземпляров попытаются создать один и тот же ресурс. Создайте эти ресурсы вне топологии и используйте Storm для чтения, записи и анализа данных.

1. Используйте протокол SSH для подключения к кластеру с помощью пользователя SSH и его пароля, указанных в шаблоне при создании кластера. Например, для подключения с помощью команды `ssh` необходимо использовать следующий синтаксис.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    Замените `sshuser` именем пользователя SSH, которое использовалось при создании кластера. Замените `clustername` именем кластера HBase.

2. Из сеанса SSH запустите оболочку HBase.
   
    ```bash
    hbase shell
    ```
   
    После загрузки оболочки вы увидите запрос `hbase(main):001:0>`.

3. В оболочке HBase введите следующую команду, чтобы создать таблицу, в которой будут храниться данные с датчиков.
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. Используйте следующую команду, чтобы проверить, создана ли таблица.
   
    ```hbase
    scan 'SensorData'
    ```
   
    Она вернет информацию, которая указывает на то, что в таблице 0 строк и выглядит примерно следующим образом.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Введите `exit` для выхода из оболочки HBase.

## <a name="configure-the-hbase-bolt"></a>Настройка сита HBase

Для записи данных в HBase из кластера Storm ситу HBase необходимо предоставить сведения о конфигурации кластера HBase.

1. Чтобы получить кворум Zookeeper для кластера HBase, используйте один из следующих примеров:

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > Замените `your_HDInsight_cluster_name` на имя вашего кластера HDInsight. См. дополнительные сведения об установке служебной программы `jq`: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
    >
    > При появлении запроса введите пароль администратора HDInsight.

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > Замените your_HDInsight_cluster_name именем своего кластера HDInsight. При появлении запроса введите пароль администратора HDInsight.
    >
    > Для этого примера требуется Azure PowerShell. См. дополнительные сведения об [установке Azure PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6).

    Возвращаемая информация выглядит приблизительно так:

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Эта информация используется Storm для обмена данными с кластером HBase.

2. Измените файл `dev.properties` и добавьте сведения о кворуме Zookeeper в следующую строку:

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Выполнение сборки, упаковка и развертывание решения в HDInsight

Выполните приведенные действия в своей среде разработки, чтобы развернуть топологию Storm в кластере Storm.

1. Из каталога `TemperatureMonitor` выполните следующую команду, чтобы создать пакет JAR из своего проекта:
   
        mvn clean package
   
    Эта команда создает файл с именем `TemperatureMonitor-1.0-SNAPSHOT.jar in the ` в целевом каталоге проекта.

2. Используйте SCP, чтобы отправить файлы `TemperatureMonitor-1.0-SNAPSHOT.jar` и `dev.properties` в кластер Storm. В следующем примере замените `sshuser` именем пользователя SSH, указанным при создании кластера, а `clustername` — именем кластера Storm. При появлении запроса введите пароль пользователя SSH.
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > Передача файла может занять несколько минут.

    Дополнительные сведения об использовании команд `scp` и `ssh` с HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Когда файл будет передан, подключитесь к кластеру Storm с помощью протокола SSH.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Замените `sshuser` именем пользователя SSH. Замените `clustername` именем пользователя кластера Storm.

4. Чтобы запустить топологию, в сеансе SSH выполните следующую команду.
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * Команда `--remote` отправляет топологию в службу Nimbus, которая распределяет ее между узлами супервизора в кластере.
    * Команда `--filter` использует файл `dev.properties` для указания значений параметров в определении топологии.
    * Команда `-R /with-hbase.yaml` использует топологию `with-hbase.yaml`, включенную в пакет.

5. После запуска топологии откройте в браузере опубликованный в Azure веб-сайт и с помощью команды `node app.js` отправьте данные в концентратор событий. Веб-панель мониторинга должна начать обновлять данные.
   
    !["Веб-транзакции"](./media/apache-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Просмотр данных HBase

Выполните приведенные ниже инструкции, чтобы подключиться к HBase и проверить, записаны ли данные в таблицу.

1. Используйте SSH, чтобы подключиться к кластеру HBase.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Замените `sshuser` именем пользователя SSH. Замените `clustername` именем кластера HBase.

2. Из сеанса SSH запустите оболочку HBase.
   
    ```bash
    hbase shell
    ```
   
    После загрузки оболочки вы увидите запрос `hbase(main):001:0>`.

3. Просмотрите строки из таблицы с помощью следующей команды.
   
    ```hbase
    scan 'SensorData'
    ```
   
    Эта команда возвращает информацию, которая указывает на то, что в таблице имеются данные. Эта информация имеет следующий вид.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > Эта операция проверки возвращает не более 10 строк из таблицы.

## <a name="delete-your-clusters"></a>Удаление кластеров

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Чтобы одновременно удалить кластеры, хранилище и веб-приложение, удалите группу ресурсов, которая их содержит.

## <a name="next-steps"></a>Дальнейшие действия

Другие примеры топологий Storm с HDInsight приведены в статье [Примеры топологий и компонентов Storm для Apache Storm в HDInsight](apache-storm-example-topology.md).

Дополнительные сведения об Apache Storm см. на сайте [Apache Storm](https://storm.incubator.apache.org/).

Дополнительные сведения о базе данных HBase в HDInsight см. в статье [Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие с BigTable, для Hadoop](../hbase/apache-hbase-overview.md).

Дополнительные сведения о библиотеке Socket.io см. на сайте [socket.io](http://socket.io/).

Дополнительные сведения о D3.js см. на странице [D3.js - Data Driven Documents](http://d3js.org/) (D3.js. Документы, управляемые данными).

Сведения о создании топологий на языке Java см. в статье [Разработка топологий на основе Java для базовых приложений подсчета слов с помощью Apache Storm и Maven в HDInsight](apache-storm-develop-java-topology.md).

Сведения о создании топологий на .NET см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
