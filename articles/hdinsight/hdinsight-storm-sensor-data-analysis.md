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
ms.date: 03/21/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 5998d785a63d43338240eabfdbbca8008a02e4b7
ms.contentlocale: ru-ru
ms.lasthandoff: 07/13/2017

---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Анализ полученных с датчиков данных с использованием Apache Storm, концентратора событий и базы данных HBase в службе HDInsight (Hadoop)

Узнайте, как с помощью Apache Storm в HDInsight можно обрабатывать данные датчиков из концентратора событий Azure. Эти данные можно сохранить в Apache HBase в HDInsight и визуализировать с помощью D3.js.

С помощью шаблона Azure Resource Manager, используемого в этом документе, показывается, как создать несколько ресурсов Azure в группе ресурсов. Этот шаблон создает виртуальную сеть Azure, два кластера HDInsight (Storm и HBase) и веб-приложение Azure. Реализация node.js панели мониторинга в реальном времени автоматически развертывается в веб-приложении.

> [!NOTE]
> Для использования сведений и примера в этом документе требуется HDInsight версии 3.5.
>
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
  
  > [!IMPORTANT]
  > Вам не нужен существующий кластер HDInsight. В этом документе описаны действия по созданию следующие ресурсов:
  > 
  > * виртуальная сеть Azure;
  > * кластер Storm в HDInsight (под управлением Linux, с двумя рабочими узлами);
  > * кластер HBase в HDInsight (под управлением Linux, с двумя рабочими узлами);
  > * веб-приложение Azure, в котором размещается панель мониторинга.

* [Node.js](http://nodejs.org/): используется для локального предварительного просмотра веб-панели мониторинга в среде разработки.
* [Java и пакет JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): используется для разработки топологии Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): используется для создания и компиляции проекта.
* [Git](http://git-scm.com/): используется для скачивания проекта с сайта GitHub.
* Клиент **SSH**: используется для подключения к кластерам HDInsight под управлением Linux. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    > [!NOTE]
    > Кроме того, необходим доступ к команде `scp`, которая используется для копирования файлов между локальной средой разработки и кластером HDInsight с помощью протокола SSH.


## <a name="architecture"></a>Архитектура

![схема архитектуры](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

В этом примере используются следующие компоненты:

* **Концентраторы событий Azure**: содержат собираемые с датчиков данные.
* **Storm в HDInsight**: в реальном времени обрабатывает данные, получаемые из концентратора событий.
* **HBase в HDInsight.**Обеспечивает постоянное хранилище данных NoSQL для данных, обработанных Storm.
* **Служба виртуальной сети Azure**: обеспечивает безопасный обмен данными между кластерами Storm в HDInsight и HBase в HDInsight.
  
  > [!NOTE]
  > Виртуальная сеть требуется при использовании клиентского API Java для HBase. Она не предоставляется через общедоступный шлюз для кластеров HBase. Установка кластеров HBase и Storm в одной виртуальной сети позволяет кластеру Storm (или любой другой системе в виртуальной сети) обращаться непосредственно к кластеру HBase с помощью клиентского API.

* **Веб-сайт панели мониторинга**: пример панели мониторинга, на которой в режиме реального времени строятся диаграммы на основе полученных данных.
  
  * Веб-сайт построен на Node.js. Его можно проверить на любой клиентской операционной системе или развернуть в службе веб-сайтов Azure.
  * [Socket.io](http://socket.io/) .
    
    > [!NOTE]
    > Использование Socket.io для связи относится к тонкостям реализации. На практике можно использовать любую платформу, например сокеты прямого доступа WebSocket или SignalR.

  * [D3.js](http://d3js.org/) .

> [!IMPORTANT]
> Требуются два кластера, так как метода для создания одного кластера HDInsight для Storm и для HBase не существует.

Топология считывает данные из концентратора событий с помощью класса [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) и записывает их в HBase с помощью класса [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/releases/1.0.1/javadocs/org/apache/storm/hbase/bolt/HBaseBolt.html). Связь с веб-сайтом реализована с использованием библиотеки [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

На следующей схеме поясняется структура топологии.

![схема топологии](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Это очень упрощенная схема топологии. Во время выполнения для каждого раздела концентратора событий, с которого считываются данные, создается экземпляр каждого компонента. Экземпляры распределяются между узлами в кластере. Обмен данными между ними выглядит следующим образом.
> 
> * Данные из «воронки» равномерно передаются в средство синтаксического анализа.
> * Данные из средства синтаксического анализа передаются в панель мониторинга и базу данных HBase. Данные группируются по идентификатору устройства, чтобы сообщения с одного устройства всегда перенаправлялись в один и тот же компонент.

### <a name="topology-components"></a>Компоненты топологии

* **Воронка концентратора событий**: входит в состав Apache Storm версии 0.10.0 и выше.
  
  > [!NOTE]
  > Для работы воронки концентратора событий, используемой в данном примере, требуется кластер Storm в HDInsight версии 3.3 или 3.4. Сведения о том, как использовать концентраторы событий с более ранней версией кластера Storm в HDInsight, можно найти в разделе [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: из воронки отправляются необработанные данные JSON. Иногда за один раз может отправляться сразу несколько событий. «Сито» определяет, как считывать данные, отправляемые из воронки, и отправляет их в новый поток в виде кортежа с несколькими полями.
* **DashboardBolt.java**: этот компонент показывает, как использовать клиентскую библиотеку Socket.io для Java для отправки данных в реальном времени в веб-панель мониторинга.
* **Temperature.java**: этот компонент определяет топологию и загружает данные конфигурации из файла **Config.properties**.

## <a name="prepare-your-environment"></a>Подготовка среды

Прежде чем использовать этот пример, необходимо создать концентратор событий Azure, из которого топология Storm будет считывать данные.

### <a name="configure-event-hub"></a>Настройка концентраторов Event Hub

В этом примере концентратор событий выступает в роли источника данных. Чтобы создать концентратор событий, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com) выберите **+ Создать** -> **Интернет вещей** -> **Концентраторы событий**.
2. В колонке **Создать пространство имен** выполните следующие задачи:
   
   1. В поле **Имя** введите имя для пространства имен.
   2. Выберите ценовую категорию. **Базовый** подойдет для этого примера.
   3. В поле **Подписка** выберите подписку Azure, которую нужно использовать.
   4. Создайте группу ресурсов или выберите имеющуюся.
   5. В поле **Расположение** выберите расположение для концентратора событий.
   6. Выберите **Закрепить на панели мониторинга** и щелкните **Создать**.

3. После завершения процесса создания отобразится колонка "Концентраторы событий" для пространства имен. В этой колонке выберите **+ Add Event Hub**(+ Добавить концентратор событий). В колонке **Создание концентратора событий** введите имя **sensordata**, а затем щелкните **Создать**. Оставьте в остальных полях значения по умолчанию.
4. В колонке "Концентраторы событий" для пространства имен выберите **Концентраторы событий**. Выберите запись **sensordata** .
5. В колонке для концентратора событий sensordata выберите **Политики общего доступа**. Воспользуйтесь ссылкой **+ Добавить** , чтобы добавить следующие политики.

    | Имя политики | Claims |
    | ----- | ----- |
    | устройства | Отправка |
    | storm | Прослушивание |

1. Выберите обе политики и запишите значение параметра **Первичный ключ** . При выполнении следующих шагов понадобятся значения обеих политик.

## <a name="download-and-configure-the-project"></a>Скачивание и настройка проекта

Для скачивания проекта с GitHub выполните следующую команду.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

После выполнения команды структура каталогов будет следующей:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                hbase-site.xml - connection information for the HBase cluster.
                Config.properties - configuration information for the topology. How to read from Event Hub and the URI to the dashboard.
            src/ - the Java bolts and topology definition.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> В этом документе не приводятся подробные сведения о коде примера. Тем не менее, код содержит полные комментарии.

Откройте файл **hdinsight-eventhub-example/TemperatureMonitor/resources/Config.properties** и добавьте сведения о концентраторе событий в следующие строки.

    eventhubspout.username = <shared access policy name that can read from Event Hub>
    eventhubspout.password = <shared access policy key>
    eventhubspout.namespace = <namespace of your Event Hub
    eventhubspout.entitypath = <name of your event hub>
    eventhubspout.partitions.count = 2

После добавления этих сведений сохраните файл.

## <a name="compile-and-test-locally"></a>Локальная компиляция и тестирование

Перед началом тестирования вам необходимо запустить панель мониторинга, чтобы просмотреть выходные данные топологии и создать данные для хранения в концентраторе событий.

> [!IMPORTANT]
> При локальном тестировании компонент HBase этой топологии неактивен. Причина этого в том, что доступ к API Java для кластера HBase нельзя получить извне виртуальной сети Azure, содержащей кластеры.

### <a name="start-the-web-application"></a>Запуск веб-приложения

1. Откройте новое окно командной строки или терминала и перейдите в каталог **hdinsight-eventhub-example/dashboard**. Чтобы установить зависимости, необходимые для веб-приложения, выполните следующую команду.
   
        npm install

2. Запустите веб-приложение с помощью следующей команды.
   
        node server.js
   
    Должно появиться сообщение следующего вида.
   
        Server listening at port 3000

3. Откройте веб-браузер и введите в адресной строке **http://localhost:3000/**. Вы должны увидеть страницу, аналогичную показанной ниже.
   
    ![веб-панель мониторинга](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Не закрывайте окно командной строки или терминала. После завершения тестирования остановите веб-сервер с помощью клавиш CTRL+C.

### <a name="start-generating-data"></a>Начало создания данных

> [!NOTE]
> Для описанных в этом разделе действий используется Node.js, поэтому эти действия могут выполняться на любой платформе. Примеры для других языков см. в каталоге **SendEvents**.

1. Откройте новое окно командной строки или терминала и перейдите в каталог **hdinsight-eventhub-example/SendEvents/nodejs**. Чтобы установить зависимости, необходимые для веб-приложения, выполните следующую команду.
   
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

3. Для добавления новых записей в концентратор событий используйте следующую команду.
   
        node app.js
   
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

### <a name="start-the-topology"></a>Запуск топологии

1. Откройте новое окно командной строки, оболочки или терминала и перейдите в каталог **hdinsight-eventhub-example/TemperatureMonitor**. Затем используйте следующую команду, чтобы запустить топологию:
   
        mvn compile exec:java
   
    Эта команда запускает топологию в локальном режиме. Значения, содержащиеся в файле **Config.properties**, предоставляют сведения о подключении для концентраторов событий и локального веб-сайта панели мониторинга. После запуска топология считывает записи из концентратора событий и отправляет их на панель мониторинга, работающую на локальном компьютере. На веб-панели мониторинга должны появиться линии, как показано на рисунке ниже.
   
    ![панель мониторинга с данными](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. Не закрывая панель мониторинга, отправьте новые данные в концентраторы событий с помощью команды `node app.js` (использовалась на предыдущих шагах). Так как значения температуры генерируются случайным образом, граф будет изменяться, отображая значительные изменения температуры.
   
   > [!NOTE]
   > Выполнять команду `node app.js` необходимо из каталога **hdinsight-eventhub-example/SendEvents/Nodejs**.

3. Убедившись, что панель мониторинга обновляется, остановите топологию, нажав клавиши CTRL+C. С помощью клавиш Ctrl+C можно также остановить локальный веб-сервер.

## <a name="create-a-storm-and-hbase-cluster"></a>Создание кластеров Storm и HBase

В действиях, описанных в этом разделе, используется [шаблон Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) для создания виртуальной сети Azure и кластеров Storm и HBase в ней. Шаблон также создает веб-приложение Azure и развертывает в нем копию панели мониторинга.

> [!NOTE]
> Виртуальная сеть используется для того, чтобы топология, запущенная на кластере Storm, могла непосредственно обмениваться данными с кластером HBase с помощью API Java для HBase.

Шаблон Resource Manager, используемый в этом документе, расположен в общедоступном контейнере BLOB-объектов по адресу **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**.

1. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Resource Manager на портале Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.5.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. В колонке **Настраиваемое развертывание** укажите следующие значения.
   
    ![Параметры HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
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

Когда указанные ресурсы будут созданы, отобразится колонка группы ресурсов, которая содержит кластеры и веб-панель мониторинга.

![Колонка группы ресурсов для виртуальной сети и кластеров](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **storm-BASENAME** и **hbase-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам. Кроме того, обратите внимание, что имя сайта панели мониторинга — **basename-dashboard**. Это значение используется ниже в этом документе.

## <a name="configure-the-dashboard-bolt"></a>Настройка сита панели мониторинга

Для отправки данных в панель мониторинга, развернутую в виде веб-приложения, необходимо изменить в файле **Config.properties** приведенную ниже строку.

    dashboard.uri: http://localhost:3000

Измените `http://localhost:3000` на `http://BASENAME-dashboard.azurewebsites.net` и сохраните файл. Замените **BASENAME** базовым именем, введенным на предыдущем шаге. Можно также использовать созданную ранее группу ресурсов, чтобы выбрать панель мониторинга и просмотреть URL-адрес.

## <a name="create-the-hbase-table"></a>Создание таблицы HBase

Чтобы хранить данные в HBase, необходимо сначала создать таблицу. Заранее создайте ресурсы, в которые Storm будет записывать данные, так как попытка создать ресурсы изнутри топологии Storm может привести к тому, что несколько экземпляров попытаются создать один и тот же ресурс. Создайте эти ресурсы вне топологии и используйте Storm для чтения, записи и анализа данных.

1. Используйте протокол SSH для подключения к кластеру с помощью пользователя SSH и его пароля, указанных в шаблоне при создании кластера. Например, для подключения с помощью команды `ssh` необходимо использовать следующий синтаксис.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    В этой команде замените **USERNAME** именем пользователя SSH, указанным при создании кластера, а **BASENAME** — указанным базовым именем. При появлении запроса введите пароль пользователя SSH.

2. Из сеанса SSH запустите оболочку HBase.
   
        hbase shell
   
    После загрузки оболочки вы увидите запрос `hbase(main):001:0>`.

3. В оболочке HBase введите следующую команду, чтобы создать таблицу, в которой будут храниться данные с датчиков.
   
        create 'SensorData', 'cf'

4. Используйте следующую команду, чтобы проверить, создана ли таблица.
   
        scan 'SensorData'
   
    Она вернет информацию, которая указывает на то, что в таблице 0 строк и выглядит примерно следующим образом.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Введите `exit` для выхода из оболочки HBase.

## <a name="configure-the-hbase-bolt"></a>Настройка сита HBase

Для записи данных в HBase из кластера Storm ситу HBase необходимо предоставить сведения о конфигурации кластера HBase. В этом примере используется файл **hbase site.xml** из кластера HBase.

### <a name="download-the-hbase-sitexml"></a>Скачивание файла hbase-site.xml

В командной строке используйте SCP, чтобы скачать файл **hbase-site.xml** из кластера. В следующем примере замените **USERNAME** именем пользователя SSH, указанным при создании кластера, а **BASENAME** — указанным ранее базовым именем. При появлении запроса введите пароль пользователя SSH. Замените `/path/to/TemperatureMonitor/resources/hbase-site.xml` путем к этому файлу в проекте TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Эта команда скачивает файл **hbase-site.xml** в указанную папку.

### <a name="enable-the-hbase-bolt"></a>Активация «сита» HBase

Чтобы включить компонент bolt HBase, откройте файл **TemperatureMonitor/src/main/java/com/microsoft/examples/Temperature.java** и раскомментируйте следующие строки.

    // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

После этого сохраните файл.

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Выполнение сборки, упаковка и развертывание решения в HDInsight

Выполните приведенные действия в своей среде разработки, чтобы развернуть топологию Storm в кластере Storm.

1. Из каталога **TemperatureMonitor** выполните следующую команду, чтобы выполнить сборку и создать новый пакет JAR из своего проекта.
   
        mvn clean compile package
   
    Эта команда создает файл **TemperatureMonitor-1.0-SNAPSHOT.jar** в **целевом** каталоге вашего проекта.

2. Используйте scp, чтобы передать файл **TemperatureMonitor-1.0-SNAPSHOT.jar** в кластер Storm. В следующем примере замените **USERNAME** именем пользователя SSH, указанным при создании кластера, а **BASENAME** — указанным ранее базовым именем. При появлении запроса введите пароль пользователя SSH.
   
        scp target/TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar

   > [!NOTE]
   > Передача файла может занять несколько минут.

3. После завершения передачи файла подключитесь к кластеру с помощью SSH.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Чтобы запустить топологию, в сеансе SSH выполните следующую команду.
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature temperature

5. После запуска топологии откройте в браузере опубликованный в Azure веб-сайт и с помощью команды `node app.js` отправьте данные в концентратор событий. Веб-панель мониторинга должна начать обновлять данные.
   
    !["Веб-транзакции"](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Просмотр данных HBase

Выполните приведенные ниже инструкции, чтобы подключиться к HBase и проверить, записаны ли данные в таблицу.

1. Используйте SSH, чтобы подключиться к кластеру HBase.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. Из сеанса SSH запустите оболочку HBase.
   
        hbase shell
   
    После загрузки оболочки вы увидите запрос `hbase(main):001:0>`.

3. Просмотрите строки из таблицы с помощью следующей команды.
   
        scan 'SensorData'
   
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
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Чтобы одновременно удалить кластеры, хранилище и веб-приложение, удалите группу ресурсов, которая их содержит.

## <a name="next-steps"></a>Дальнейшие действия

Другие примеры топологий Storm с HDInsight приведены в статье [Примеры топологий и компонентов Storm для Apache Storm в HDInsight](hdinsight-storm-example-topology.md).

Дополнительные сведения об Apache Storm см. на сайте [Apache Storm](https://storm.incubator.apache.org/).

Дополнительные сведения о базе данных HBase в HDInsight см. в статье [Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие с BigTable, для Hadoop](hdinsight-hbase-overview.md).

Дополнительные сведения о библиотеке Socket.io см. на сайте [socket.io](http://socket.io/).

Дополнительные сведения о D3.js см. на странице [D3.js - Data Driven Documents](http://d3js.org/) (D3.js. Документы, управляемые данными).

Сведения о создании топологий на языке Java см. в статье [Разработка топологий на основе Java для базовых приложений подсчета слов с помощью Apache Storm и Maven в HDInsight](hdinsight-storm-develop-java-topology.md).

Сведения о создании топологий на .NET см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com

