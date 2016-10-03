<properties
   pageTitle="Мониторинг кластеров HDInsight и управление ими с помощью интерфейса REST API Ambari | Microsoft Azure"
   description="Узнайте, как использовать Ambari для наблюдения за кластерами HDInsight на платформе Linux и управления их функционированием. В этом документе рассказывается об использовании интерфейса REST API Ambari, предоставляемого с кластерами HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#Управление кластерами HDInsight с помощью REST API Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari упрощает управление кластером Hadoop и его мониторинг за счет удобного пользовательского веб-интерфейса и интерфейса REST API. Ambari предоставляется с кластерами HDInsight под управлением Linux и используется для мониторинга кластера и внесения изменений в его конфигурацию. В этом документе вы узнаете об основных принципах работы с REST API Ambari, выполнив типичные задачи с помощью cURL.

##Предварительные требования

* [cURL](http://curl.haxx.se/). cURL — это кроссплатформенная служебная программа, которая может использоваться для работы с интерфейсами REST API из командной строки. В этом документе она используется для взаимодействия с REST API Ambari.
* [jq](https://stedolan.github.io/jq/). jq — это кроссплатформенная служебная программа командной строки для работы с документами JSON. В этом документе она используется для анализа документов JSON, возвращенных из REST API Ambari.
* [Azure CLI](../xplat-cli-install.md) — это кроссплатформенная служебная программа командной строки для работы со службами Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a id="whatis"></a>Что такое Ambari

[Apache Ambari](http://ambari.apache.org) упрощает управление Hadoop, предоставляя простой в использовании веб-интерфейс, который можно применять для подготовки и мониторинга кластеров Hadoop, а также управления их функционированием. Разработчики могут интегрировать эти возможности в своих приложениях с помощью [интерфейсов REST API Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari предоставляется по умолчанию с кластерами HDInsight на платформе Linux.

##Интерфейс REST API

Базовый универсальный код ресурса (URI) для интерфейса REST API Ambari в HDInsight: https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, где __CLUSTERNAME__ — это имя кластера.

> [AZURE.IMPORTANT] Хотя имя кластера в полном доменном имени в URI (CLUSTERNAME.azurehdinsight.net) указывается без учета регистра, другие вхождения этого URI учитывают регистр. Например, если кластер называется MyCluster, допустимые URI имеют следующий вид:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> Приведенные ниже URI возвратят ошибку, так как второе вхождение имени имеет неправильный регистр.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Подключение к Ambari в HDInsight выполняется по протоколу HTTPS. При проверке подлинности подключения необходимо использовать имя учетной записи администратора (по умолчанию используется __admin__) и пароль, указанные при создании кластера.

Ниже приведен пример использования cURL для выполнения запроса GET к REST API. Замените __PASSWORD__ паролем администратора кластера. Замените __CLUSTERNAME__ именем кластера.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
В результате возвращается документ JSON, который начинается со следующих сведений:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Так как это сведения в формате JSON, для работы с данными удобнее использовать средство синтаксического анализа JSON. Например, в следующем примере используется jq для отображения только элемента `health_report`.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##Пример. Получение полного доменного имени для узлов кластера

При работе с HDInsight вам может потребоваться полное доменное имя (FQDN) узла кластера. FQDN можно легко получить для различных узлов в кластере с помощью следующих команд:

* __Головные узлы__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Рабочие узлы__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Узлы Zookeeper__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Обратите внимание, что в каждом из этих примеров используется тот же шаблон:

1. Отправка запроса к компоненту, выполняющемуся на этих узлах.
2. Получение элементов `host_name`, содержащих полное доменное имя для этих узлов.

Элемент `host_components` документа возврата содержит несколько элементов. Используйте `.host_components[]`, а затем укажите путь внутри элемента, чтобы пройтись по каждому элементу и извлечь значение из конкретного пути. Если требуется только одно значение, например первая запись FQDN, можно вернуть элементы в виде коллекции и выбрать определенную запись:

    jq '[.host_components[].HostRoles.host_name][0]'

Эта команда возвращает первое полное доменное имя из коллекции.

##Пример. Получение учетной записи хранения и контейнера по умолчанию

При создании кластера HDInsight необходимо использовать учетную запись хранения Azure и контейнер больших двоичных объектов в качестве хранилища по умолчанию для кластера. Для получения этой информации после создания кластера можно использовать Ambari. Например, это можно сделать, если требуется программно записать данные непосредственно в контейнер.

Следующая команда получит универсальный код ресурса (URI) WASB хранилища кластеров по умолчанию:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Эта команда возвращает первую конфигурацию, применяемую к серверу (`service_config_version=1`), который содержит эти сведения. Чтобы получить значение, которое было изменено после создания кластера, может потребоваться перечислить версии конфигурации и получить последнюю из них.

Будет возвращено значение, аналогичное приведенному ниже, где __CONTAINER__ — это контейнер по умолчанию, а __ACCOUNTNAME__ — имя учетной записи хранения Azure.

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Затем эти сведения можно использовать с [интерфейсом командной строки Azure](../xplat-cli-install.md) для отправки или загрузки данных из контейнера.

1. Получите группу ресурсов для учетной записи хранения. Замените __ACCOUNTNAME__ именем учетной записи хранения, полученным из Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Эта команда возвращает имя группы ресурсов для учетной записи.
    
    > [AZURE.NOTE] Если эта команда не возвращает данные, может потребоваться изменить режим интерфейса командной строки Azure на режим диспетчера ресурсов Azure и запустить команду еще раз. Чтобы переключиться в режим Azure Resource Manager, выполните следующую команду:
    >
    > `azure config mode arm`
    
2. Получите ключ для учетной записи хранения. Замените __GROUPNAME__ именем группы ресурсов, полученным на предыдущем шаге. Замените __ACCOUNTNAME__ именем учетной записи хранения:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Этот пример возвращает первичный ключ для учетной записи.
    
3. Используйте команду upload, чтобы сохранить файл в контейнере:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Замените __ACCOUNTNAME__ именем учетной записи хранения. Замените __ACCOUNTKEY__ ключом, полученным ранее. __FILEPATH__ — это путь к файлу, который необходимо передать, а __BLOBPATH__ — путь в контейнере.

    Например, если файл должен отображаться в HDInsight по адресу wasbs://example/data/filename.txt, путь __BLOBPATH__ будет выглядеть так: `example/data/filename.txt`.

##Пример: изменение конфигурации Ambari

1. Получите текущую конфигурацию, которую Ambari хранит в виде "требуемой конфигурации":

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Этот пример возвращает документ JSON с текущей конфигурацией (идентифицируется по значению _tag_) для компонентов, установленных в кластере. Ниже приведен пример фрагмента данных, возвращаемых из типа кластера Spark.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    Из этого списка нужно скопировать имя компонента (например, __spark\_thrift\_sparkconf__) и значение __tag__.
    
2. Извлеките конфигурацию для компонента и значение tag с помощью следующей команды: Замените __spark-thrift-sparkconf__ и __INITIAL__ на компонент и значение tag, по которым хотите получить конфигурацию.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curl извлекает документ JSON, а затем, чтобы внести изменения в данные для создания шаблона, используется jq. Созданный шаблон применяется для добавления и изменения значений конфигурации. В частности, он выполняет следующие действия:
    
    * Создает уникальное значение, содержащее строку version и дату, которая хранится в __newtag__.
    * Создает корневой документ для новой требуемой конфигурации.
    * Возвращает содержимое массива `.items[]` и добавляет его в элемент __desired\_config__.
    * Удаляет элементы __href__, __version__ и __Config__, так как они не нужны для отправки новой конфигурации.
    * Добавляет новый элемент __tag__ и задает для него значение __version#################__. Числовая часть основана на текущей дате. Каждая конфигурация должна иметь уникальное значение tag.
    
    Наконец, данные сохраняются в документе __newconfig.json__. Структура документа должна выглядеть следующим образом:
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Откройте документ __newconfig.json__ и измените или добавьте значения в объекте __properties__. В следующем примере значение __spark.yarn.am.memory__ изменяется с __1g__ на __3g__ и добавляется новый элемент для __spark.kryoserializer.buffer.max__ со значением __256m__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    После завершения работы сохраните файл.

4. Выполните следующую команду, чтобы отправить измененную конфигурацию в Ambari:

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Эта команда передает содержимое файла __newconfig.json__ в cURL-запрос, который отправляет его в кластер в качестве новой требуемой конфигурации. Запрос cURL возвращает документ JSON. Элемент __versionTag__ в этом документе должен совпадать с отправленной версией, а объект __configs__ содержит запрошенные изменения конфигурации.

###Пример. Перезапуск компонента службы

Если на этом этапе взглянуть на веб-интерфейс Ambari, вы узнаете, что необходимо перезапустить службу Spark, чтобы новая конфигурация вступила в силу. Для перезапуска службы выполните приведенные ниже действия.

1. Выполните следующую команду, чтобы перевести службу Spark в режим обслуживания.

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Она отправляет документ JSON на сервер (содержится в инструкции `echo`) для включения режима обслуживания. Проверить, находится ли служба в этом режиме, можно с помощью следующего запроса.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    При этом возвращается значение `"ON"`.

3. Затем выполните следующую команду, чтобы отключить службу.

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Эта команда возвращает ответ следующего вида:
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    Значение `href`, возвращенное этим универсальным кодом ресурса (URI), использует внутренний IP-адрес узла кластера. Чтобы использовать его извне кластера, замените часть "10.0.0.18:8080" на полное доменное имя кластера. Например, следующая команда получает состояние запроса.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Если это значение возвращает `"COMPLETED"`, то запрос завершен.

4. После завершения предыдущего запроса используйте следующую команду для запуска службы.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    После перезапуска службы будут использоваться новые параметры конфигурации.

5. Наконец, используйте следующую команду для отключения режима обслуживания.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##Дальнейшие действия

Полный справочник по REST API см. в статье [Справочник по API Ambari версии 1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Так как интерфейсом Ambari управляет облачная служба HDInsight, некоторые функции (например, добавление узлов в кластер, удаление их из него или добавление новых служб) отключены.

<!---HONumber=AcomDC_0921_2016-->