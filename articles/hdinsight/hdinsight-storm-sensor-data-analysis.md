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
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ec3384dd68a28117cc00de20e4c08ad0ccd67bad
ms.openlocfilehash: 92e4094c5125d922a106c65d2cb3cd5b654856a5


---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Анализ полученных с датчиков данных с использованием Apache Storm, концентратора событий и базы данных HBase в службе HDInsight (Hadoop)
Узнайте, как с помощью Apache Storm в HDInsight можно обрабатывать данные датчиков из концентратора событий Azure, сохранять их в Apache HBase в HDInsigh и визуализировать с помощью приложения D3.js, запущенного как веб-приложение Azure.

С помощью шаблона Azure Resource Manager, используемого в этом документе, показывается, как создать несколько ресурсов Azure в группе ресурсов. А именно, этот шаблон создает виртуальную сеть Azure, два кластера HDInsight (Storm и HBase) и веб-приложение Azure. Реализация node.js панели мониторинга в реальном времени автоматически развертывается в веб-приложении.

> [!NOTE]
> Сведения в этом документе и приведенный пример были проверены на кластерах HDInsight версий 3.3 и 3.4 под управлением Linux.
> 
> 

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
  
  > [!IMPORTANT]
  > Вам не нужен существующий кластер HDInsight. Действия, описанные в этом документе, позволяют создать следующие ресурсы:
  > 
  > * виртуальная сеть Azure;
  > * кластер Storm в HDInsight (под управлением Linux, с 2 рабочими узлами);
  > * кластер HBase в HDInsight (под управлением Linux, с 2 рабочими узлами);
  > * веб-приложение Azure, в котором размещается панель мониторинга.
  > 
  > 
* [Node.js](http://nodejs.org/): используется для локального предварительного просмотра панели мониторинга в среде разработки.
* [Java и пакет JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): используется для разработки топологии Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): используется для создания и компиляции проекта.
* [Git](http://git-scm.com/): используется для скачивания проекта с сайта GitHub.
* Клиент **SSH**: используется для подключения к кластерам HDInsight под управлением Linux. Дополнительные сведения об использовании SSH с HDInsight см. в приведенных ниже документах.
  
  * [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
  * [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    > [!NOTE]
    > Кроме того, необходим доступ к команде `scp`, которая используется для копирования файлов между локальной средой разработки и кластером HDInsight с помощью протокола SSH.
    > 
    > 

## <a name="architecture"></a>Архитектура
![схема архитектуры](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

В этом примере используются следующие компоненты:

* **Концентраторы событий Azure**: содержат собираемые с датчиков данные. В примере используется приложение, генерирующее эти данные.
* **Storm в HDInsight**: в реальном времени обрабатывает данные, получаемые из концентратора событий.
* **HBase в HDInsight.**Обеспечивает постоянное хранилище данных NoSQL для данных, обработанных Storm.
* **Служба виртуальной сети Azure**: обеспечивает безопасный обмен данными между кластерами Storm в HDInsight и HBase в HDInsight.
  
  > [!NOTE]
  > Виртуальная сеть нужна для использования API клиента Java HBase, так как он не предоставляется для кластеров HBase через общедоступный шлюз. Установка кластеров HBase и Storm в одной виртуальной сети позволяет кластеру Storm (или любой другой системе в виртуальной сети) обращаться непосредственно к кластеру HBase с помощью API клиента.
  > 
  > 
* **Веб-сайт панели мониторинга**: пример панели мониторинга, на которой в режиме реального времени строятся диаграммы на основе полученных данных.
  
  * Веб-сайт построен на Node.js. Его можно проверить на любой клиентской операционной системе или развернуть в службе веб-сайтов Azure.
  * [Socket.io](http://socket.io/) .
    
    > [!NOTE]
    > Это сведения о реализации примера. На практике можно использовать любую платформу, например сокеты прямого доступа WebSocket или SignalR.
    > 
    > 
  * [D3.js](http://d3js.org/) .

> [!IMPORTANT]
> Требуются два кластера, так как метода для создания одного кластера HDInsight для Storm и для HBase не существует.
> 
> 

Топология считывает данные из концентратора событий с помощью класса [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) и записывает их в HBase с помощью класса [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). Связь с веб-сайтом реализована с использованием библиотеки [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Схема топологии выглядит так.

![схема топологии](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Это очень упрощенная схема топологии. Во время выполнения для каждого раздела концентратора событий, с которого считываются данные, создается экземпляр каждого компонента. Экземпляры распределяются между узлами в кластере. Обмен данными между ними выглядит следующим образом.
> 
> * Данные из «воронки» равномерно передаются в средство синтаксического анализа.
> * Данные из средства синтаксического анализа передаются в панель мониторинга и базу данных HBase. Данные группируются по идентификатору устройства, чтобы сообщения с одного устройства всегда перенаправлялись в один и тот же компонент.
> 
> 

### <a name="topology-components"></a>Компоненты топологии
* **Воронка концентратора событий**: входит в состав Apache Storm версии 0.10.0 и выше.
  
  > [!NOTE]
  > Для работы воронки концентраторов событий, используемой в данном примере, требуется кластер Storm в HDInsight версии 3.3 или 3.4. Сведения о том, как использовать концентраторы событий с более ранней версией кластера Storm в HDInsight, можно найти в разделе [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).
  > 
  > 
* **ParserBolt.java**: из воронки отправляются необработанные данные JSON. Иногда за один раз может отправляться сразу несколько событий. «Сито» определяет, как считывать данные, отправляемые из воронки, и отправляет их в новый поток в виде кортежа с несколькими полями.
* **DashboardBolt.java**: определяет, как использовать клиентскую библиотеку Socket.io для Java для отправки данных в реальном времени в веб-панель мониторинга.

В этом примере используется платформа [Flux](https://storm.apache.org/releases/0.10.0/flux.html) , поэтому определение топологии содержится в файлах YAML. Их всего два:

* **hbase.yaml нет** — используйте этот файл при тестировании топологии в среде разработки. В нем не используются компоненты HBase, так как невозможно получить доступ к API Java для HBase извне виртуальной сети, в которой находится кластер.
* **with-hbase.yaml** — используйте этот файл при развертывании топологии в кластере Storm. В нем используются компоненты HBase, так как он выполняется в той же виртуальной сети, что и кластер.

## <a name="prepare-your-environment"></a>Подготовка среды
Прежде чем использовать этот пример, необходимо создать концентратор событий Azure, из которого топология Storm будет считывать данные.

### <a name="configure-event-hub"></a>Настройка концентраторов Event Hub
В этом примере концентратор событий выступает в роли источника данных. Чтобы создать новый концентратор Event Hub, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com) выберите **+ Создать** -> **Интернет вещей** -> **Концентраторы событий**.
2. В колонке **Создать пространство имен** выполните следующие задачи:
   
   1. В поле **Имя** введите имя для пространства имен.
   2. Выберите ценовую категорию. **Базовый** подойдет для этого примера.
   3. В поле **Подписка** выберите подписку Azure, которую нужно использовать.
   4. Создайте группу ресурсов или выберите имеющуюся.
   5. В поле **Расположение** выберите расположение для концентратора событий.
   6. Выберите **Закрепить на панели мониторинга** и щелкните **Создать**.
3. После завершения процесса создания отобразится колонка "Концентраторы событий" для пространства имен. В этой колонке выберите **+ Add Event Hub**(+ Добавить концентратор событий). В колонке **Create Event Hub** (Создание концентратора событий) введите имя **sensordata**, а затем щелкните **Создать**. Оставьте в остальных полях значения по умолчанию.
4. В колонке "Концентраторы событий" для пространства имен выберите **Концентраторы событий**. Выберите запись **sensordata** .
5. В колонке для концентратора событий sensordata выберите **Политики общего доступа**. Воспользуйтесь ссылкой **+ Добавить** , чтобы добавить следующие политики.

    | Имя политики | Claims |
    | ----- | ----- |
    | устройства | Отправка |
    | storm | Прослушивание |

1. Выберите обе политики и запишите значение параметра **Первичный ключ** . При выполнении следующих шагов вам понадобятся значения обеих политик.

## <a name="download-and-configure-the-project"></a>Скачивание и настройка проекта
Для скачивания проекта с GitHub выполните следующую команду.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

После выполнения команды у вас будет следующая структура каталогов.

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [!NOTE]
> Мы не приводим подробные сведения о коде примера, так как весь код сопровожден комментариями.
> 
> 

Откройте файл **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** и добавьте сведения о концентраторе событий в следующих строках.

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [!NOTE]
> В этом примере предполагается, что вы использовали **storm** в качестве имени политики с утверждением **Listen** (Прослушивание), а концентратор событий называется **sensordata**.
> 
> 

 После добавления этих сведений сохраните файл.

## <a name="compile-and-test-locally"></a>Локальная компиляция и тестирование
Перед началом тестирования вам необходимо запустить панель мониторинга, чтобы просмотреть выходные данные топологии и создать данные для хранения в концентраторе событий.

> [!IMPORTANT]
> При локальном тестировании компонент HBase этой топологии неактивен, так как доступ к API Java для кластера HBase нельзя получить извне виртуальной сети Azure, содержащей кластеры.
> 
> 

### <a name="start-the-web-application"></a>Запуск веб-приложения
1. Откройте новое окно командной строки или терминала и перейдите в каталог **hdinsight-eventhub-example/dashboard**. Затем установите необходимые для веб-приложения зависимости, используя следующую команду:
   
        npm install
2. Запустите веб-приложение с помощью следующей команды.
   
        node server.js
   
    Должно появиться примерно такое сообщение:
   
        Server listening at port 3000
3. Откройте веб-браузер и введите в адресной строке **http://localhost:3000/**. Вы должны увидеть страницу, аналогичную показанной ниже:
   
    ![веб-панель мониторинга](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Не закрывайте окно командной строки или терминала. После завершения тестирования остановите веб-сервер с помощью клавиш CTRL+C.

### <a name="start-generating-data"></a>Начало создания данных
> [!NOTE]
> Для описанных в этом разделе действий используется Node.js, поэтому эти действия могут выполняться на любой платформе. Примеры для других языков см. в каталоге **SendEvents**.
> 
> 

1. Откройте новое окно командной строки, оболочки или терминала и перейдите в каталог **hdinsight-eventhub-example/SendEvents/nodejs**. Затем установите необходимые для приложения зависимости, используя следующую команду:
   
        npm install
2. Откройте файл **app.js** в текстовом редакторе и добавьте полученные ранее сведения о концентраторе событий.
   
        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
   
   > [!NOTE]
   > В этом примере предполагается, что имя **sensordata** использовалось в качестве имени концентратора событий, а **devices** — в качестве имени политики с утверждением **Send** (Отправка).
   > 
   > 
3. Для добавления новых записей в концентратор событий используйте следующую команду.
   
        node app.js
   
    Вы должны увидеть несколько строк выходных данных, среди которых будет информация, отправленная в концентратор событий. Эти строки будут выглядеть примерно так:
   
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

### <a name="start-the-topology"></a>Запуск топологии
1. Откройте новое окно командной строки, оболочки или терминала и перейдите в каталог **hdinsight-eventhub-example/TemperatureMonitor**. Затем используйте следующую команду, чтобы запустить топологию:
   
        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
   
    При использовании PowerShell воспользуйтесь следующей командой:
   
        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"
   
   > [!NOTE]
   > Если используется система Linux, Unix или OS X, а в [среде разработки установлен Storm](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), вместо этого можно использовать следующие команды:
   > 
   > `mvn compile package`
   > `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`
   > 
   > 
   
    В результате запустятся топологии, определенные в файле **no-hbase.yaml** , в локальном режиме. Значения, содержащиеся в файле **dev.properties** , предоставляют сведения о подключении для концентраторов событий. После запуска топология считывает записи из концентратора событий и отправляет их на панель мониторинга, работающую на локальном компьютере. На веб-панели мониторинга должны появиться линии примерно, как на рисунке ниже.
   
    ![панель мониторинга с данными](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)
2. Не закрывая панель мониторинга, отправьте новые данные в концентраторы событий с помощью команды `node app.js` (использовалась на предыдущих шагах). Так как значения температуры генерируются случайным образом, граф будет изменяться, отображая значительные изменения температуры.
   
   > [!NOTE]
   > Выполнять команду `node app.js` необходимо из каталога **hdinsight-eventhub-example/SendEvents/Nodejs**.
   > 
   > 
3. Убедившись, что все работает, остановите топологию, нажав клавиши CTRL+C. С помощью клавиш Ctrl+C можно также остановить локальный веб-сервер.

## <a name="create-a-storm-and-hbase-cluster"></a>Создание кластеров Storm и HBase
Чтобы запустить топологию в HDInsight и включить сито HBase, необходимо создать новые кластеры Storm и HBase. В действиях, описанных в этом разделе, используется [шаблон Azure Resource Manager](../resource-group-template-deploy.md) для создания новой виртуальной сети Azure и кластеров Storm и HBase в ней. Шаблон также создает веб-приложение Azure и развертывает в нем копию панели мониторинга.

> [!NOTE]
> Виртуальная сеть используется для того, чтобы топология, запущенная на кластере Storm, могла непосредственно обмениваться данными с кластером HBase с помощью API Java для HBase.
> 
> 

Шаблон Resource Manager, используемый в этом документе, расположен в общедоступном контейнере BLOB-объектов по адресу **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**.

1. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Resource Manager на портале Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. В колонке **Параметры** задайте следующие параметры.
   
    ![Параметры HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **BASECLUSTERNAME**: это значение будет использоваться в качестве базового имени для кластеров Storm и HBase. Например, если ввести **hdi**, будет создан кластер Storm **storm-hdi** и кластер HBase **hbase-hdi**.
   * **CLUSTERLOGINUSERNAME**: имя администратора для кластеров Storm и HBase.
   * **CLUSTERLOGINPASSWORD**: пароль администратора для кластеров Storm и HBase.
   * **SSHUSERNAME**: создаваемый пользователь SSH для кластеров Storm и HBase.
   * **SSHPASSWORD**: пароль пользователя SSH для кластеров Storm и HBase.
   * **LOCATION**: регион, в котором будут созданы кластеры.
     
     Нажмите кнопку **ОК** , чтобы сохранить параметры.
3. В разделе **Группа ресурсов** создайте новую группу ресурсов или выберите существующую.
4. В раскрывающемся меню **Расположение группы ресурсов** выберите расположение, указанное в параметре **LOCATION**.
5. Выберите **Юридические условия**, а затем щелкните **Создать**.
6. Наконец установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Создать**. Создание кластеров займет около 20 минут.

После создания указанных ресурсов отобразится колонка группы ресурсов, содержащей кластеры и веб-панель мониторинга.

![Колонка группы ресурсов для виртуальной сети и кластеров](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **storm-BASENAME** и **hbase-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже, для подключения к кластерам. Кроме того, обратите внимание, что имя сайта панели мониторинга — **basename-dashboard**. Оно будет использовано позже, при просмотре панели мониторинга.
> 
> 

## <a name="configure-the-dashboard-bolt"></a>Настройка сита панели мониторинга
Для отправки данных на панель мониторинга, развернутую в виде веб-приложения, необходимо изменить в файле **dev.properties** следующую строку:

    dashboard.uri: http://localhost:3000

Измените `http://localhost:3000` на `http://BASENAME-dashboard.azurewebsites.net` и сохраните файл. Замените **BASENAME** базовым именем, введенным на предыдущем шаге. Можно также использовать созданную ранее группу ресурсов, чтобы выбрать панель мониторинга и просмотреть URL-адрес.

## <a name="create-the-hbase-table"></a>Создание таблицы HBase
Чтобы хранить данные в HBase, необходимо сначала создать таблицу. Обычно ресурсы, в которые Storm будет записывать данные, создаются заранее, так как попытка создать ресурсы внутри топологии Storm может привести к порождению нескольких распределенных копий кода для создания одного и того же ресурса. Создайте эти ресурсы вне топологии и просто используйте Storm для чтения, записи и анализа данных.

1. Используйте протокол SSH для подключения к кластеру с помощью пользователя SSH и его пароля, указанных в шаблоне при создании кластера. Например, для подключения с помощью команды `ssh` необходимо использовать следующий синтаксис.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    В этой команде замените **USERNAME** именем пользователя SSH, указанным при создании кластера, а **BASENAME** — указанным базовым именем. При появлении запроса введите пароль пользователя SSH.
2. Из сеанса SSH запустите оболочку HBase.
   
        hbase shell
   
    После загрузки оболочки вы увидите запрос `hbase(main):001:0>` .
3. В оболочке HBase введите следующую команду, чтобы создать таблицу, в которой будут храниться данные с датчиков.
   
        create 'SensorData', 'cf'
4. Используйте следующую команду, чтобы проверить, создана ли таблица.
   
        scan 'SensorData'
   
    Она вернет информацию, которая указывает на то, что в таблице 0 строк и выглядит примерно следующим образом.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds
5. Чтобы выйти из оболочки HBase, введите следующую команду.
   
        exit

## <a name="configure-the-hbase-bolt"></a>Настройка сита HBase
Для записи данных в HBase из кластера Storm ситу HBase необходимо предоставить сведения о конфигурации кластера HBase. Для этого проще всего скачать файл **hbase site.xml** из кластера и добавить его в проект. Необходимо также раскомментировать несколько зависимостей в файле **pom.xml** , который загружает компонент storm-hbase и необходимые зависимости.

> [!IMPORTANT]
> Кроме того, нужно скачать файл storm-hbase.jar, доступный в кластере Storm в HDInsight 3.3 или 3.4. Эта версия скомпилирована для работы с HBase версии 1.1.x, которая используется для кластеров HBase в HDInsight 3.3 и 3.4. Если использовать компонент storm-hbase из другого источника, то может оказаться, что он скомпилирован для более ранней версии HBase.
> 
> 

### <a name="download-the-hbase-sitexml"></a>Скачивание файла hbase-site.xml
В командной строке используйте SCP, чтобы скачать файл **hbase-site.xml** из кластера. В следующем примере замените **USERNAME** именем пользователя SSH, указанным при создании кластера, а **BASENAME** — указанным ранее базовым именем. При появлении запроса введите пароль пользователя SSH. Замените `/path/to/TemperatureMonitor/resources/hbase-site.xml` путем к этому файлу в проекте TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Тогда файл **hbase-site.xml** будет скачан в указанную папку.

### <a name="download-and-install-the-storm-hbase-component"></a>Скачивание и установка компонента storm-hbase
1. В командной строке используйте SCP, чтобы скачать файл **storm-hbase.jar** из кластера Storm. В следующем примере замените **USERNAME** именем пользователя SSH, указанным при создании кластера, а **BASENAME** — указанным ранее базовым именем. При появлении запроса введите пароль пользователя SSH.
   
        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .
   
    Будет скачан файл `storm-hbase-####.jar`, где ### — номер версии Storm для кластера. Запишите этот номер, так как он будет использован позже.
2. Используйте следующую команду, чтобы установить этот компонент в локальный репозиторий Maven в вашей среде разработки. Это позволит Maven найти этот пакет при компиляции проекта. Замените **####** номером версии, указанным в имени файла.
   
        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
   
    При использовании PowerShell воспользуйтесь следующей командой:
   
        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

### <a name="enable-the-storm-hbase-component-in-the-project"></a>Добавление компонента storm-hbase в проект
1. Откройте файл **TemperatureMonitor/pom.xml** и удалите следующие строки.
   
        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
   
   > [!IMPORTANT]
   > Удалите только эти две строки. Не удаляйте строки, находящиеся между ними.
   > 
   > 
   
    Это позволит добавить несколько компонентов, которые необходимы для обмена данными с HBase с помощью сита HBase.
2. Найдите следующие строки и замените **####** номером версии скачанного ранее файла storm-hbase.
   
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>
   
   > [!IMPORTANT]
   > Номер версии должен соответствовать версии, использованной при установке компонента в локальный репозиторий Maven, так как Maven использует эту информацию для загрузки компонента при сборке проекта.
   > 
   > 
3. Сохраните файл **pom.xml** .

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Выполнение сборки, упаковка и развертывание решения в HDInsight
Выполните приведенные действия в своей среде разработки, чтобы развернуть топологию Storm в кластере Storm.

1. Из каталога **TemperatureMonitor** выполните следующую команду, чтобы выполнить сборку и создать новый пакет JAR из своего проекта.
   
        mvn clean compile package
   
    Это создаст файл под именем **TemperatureMonitor-1.0-SNAPSHOT.jar** in the **целевом** каталоге вашего проекта.
2. Используйте scp, чтобы передать файл **TemperatureMonitor-1.0-SNAPSHOT.jar** в кластер Storm. В следующем примере замените **USERNAME** именем пользователя SSH, указанным при создании кластера, а **BASENAME** — указанным ранее базовым именем. При появлении запроса введите пароль пользователя SSH.
   
        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
   
   > [!NOTE]
   > Передача файла может занять несколько минут, так как его размер составит несколько мегабайтов.
   > 
   > 
   
    Используйте SCP, чтобы отправить файл **dev.properties** , так как он содержит сведения, необходимые для подключения к концентраторам событий и панели мониторинга.
   
        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
3. После завершения передачи файла подключитесь к кластеру с помощью SSH.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
4. В сеансе SSH используйте следующую команду, чтобы запустить топологию.
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
   
    В результате этого запускается топология, использующая определение топологии в файле **with-hbase.yaml** и значения конфигурации в файле **dev.properties**.
5. После запуска топологии откройте в браузере опубликованный в Azure веб-сайт и с помощью команды `node app.js` отправьте данные в концентратор событий. Веб-панель мониторинга должна начать обновлять данные.
   
    !["Веб-транзакции"](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Просмотр данных HBase
После передачи данных в топологию с помощью `node app.js`выполните следующие действия, чтобы подключиться к HBase и убедиться, что данные записаны в таблицу, созданную ранее.

1. Используйте SSH, чтобы подключиться к кластеру HBase.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
2. Из сеанса SSH запустите оболочку HBase.
   
        hbase shell
   
    После загрузки оболочки вы увидите запрос `hbase(main):001:0>` .
3. Просмотрите строки из таблицы с помощью следующей команды.
   
        scan 'SensorData'
   
    Она вернет информацию следующего вида, указывающую на то, что в таблице имеется 0 строк.
   
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
   > 
   > 

## <a name="delete-your-clusters"></a>Удаление кластеров
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Чтобы одновременно удалить кластеры, хранилище и веб-приложение, удалите группу ресурсов, которая их содержит.

## <a name="next-steps"></a>Дальнейшие действия
Вы узнали, как использовать Storm для чтения данных из концентраторов событий, хранить эти данные в HBase и визуализировать их на внешней панели мониторинга с помощью Socket.io и D3.js.

* Дополнительные примеры топологий Storm со службой HDinsight см. в статье:
  
  * [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)
* Дополнительные сведения об Apache Storm см. на сайте [Apache Storm](https://storm.incubator.apache.org/).
* Дополнительные сведения о базе данных HBase в HDInsight см. в статье [Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие с BigTable, для Hadoop](hdinsight-hbase-overview.md).
* Дополнительные сведения о библиотеке Socket.io см. на сайте [socket.io](http://socket.io/).
* Дополнительные сведения о D3.js см. на странице [D3.js - Data Driven Documents](http://d3js.org/) (D3.js. Документы, управляемые данными).
* Сведения о создании топологий на языке Java см. в статье [Разработка топологий на основе Java для базовых приложений подсчета слов с помощью Apache Storm и Maven в HDInsight](hdinsight-storm-develop-java-topology.md).
* Сведения о создании топологий на .NET см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com



<!--HONumber=Dec16_HO3-->


